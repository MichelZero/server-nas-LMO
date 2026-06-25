# Guia de Configuração: Proxmox VE (2 SAS + 16 SATA)
**Especificações do Servidor:** 2x CPUs Xeon (24 Threads) | 128 GB RAM | 2x SAS 300GB | 16x SATA 1TB

---

## 1. Instalação do Sistema Operacional (Discos SAS)
Para garantir alta disponibilidade e isolamento do Proxmox VE, a instalação deve ser feita nos discos corporativos SAS usando espelhamento via software.

*   Inicie o servidor através da **ISO oficial** do Proxmox 9.x.
*   Na tela de seleção de discos, clique no botão **Options**.
*   No campo *Filesystem*, altere para **zfs (RAID1)**.
*   Selecione os **2 discos SAS de 300 GB**.
*   Prossiga com as configurações de rede e conclua a instalação.

---

## 2. Criação do Pool de Armazenamento das VMs (Discos SATA)
O formato RAID10 (Stripe de Espelhos) foi o escolhido por entregar o melhor desempenho de leitura/escrita aleatória e segurança para ambientes de virtualização.

### Configuração via Interface Web:
1. Acesse a interface gráfica do Proxmox no navegador.
2. No menu esquerdo, selecione o nó físico (**pve-01**).
3. Vá em **Disks** > **ZFS**.
4. Na barra superior, clique em **Create: ZFS**.
5. Preencha a janela pop-up com os seguintes parâmetros:
   *   **Name:** `vms-storage`
   *   **RAID Level:** `RAID10`
   *   **Compression:** `on` *(padrão lz4)*
   *   **ashift:** `12` *(padrão para blocos de 4K)*
   *   **Add Storage:** `Marcado` *(adiciona automaticamente ao Proxmox)*
6. Na lista de discos abaixo, **marque a caixa de seleção dos 16 discos SATA de 1 TB** *(atenção para não marcar os discos SAS do sistema)*.
7. Clique em **Create**.

---

## 3. Otimização da Memória RAM (Limitando o Cache ARC do ZFS)
Como o servidor possui 128 GB de RAM e 16 discos ativos, o ZFS tentará consumir até 50% da memória total para cache por padrão. Para liberar mais de 100 GB exclusivamente para as suas VMs, limite o cache do ZFS para **16 GB**.

Abra o **Shell (Terminal)** do Proxmox e execute os seguintes comandos:

```bash
# 1. Define o limite do cache ARC para 16 GB (17179869184 bytes)
echo "options zfs zfs_arc_max=17179869184" > /etc/modprobe.d/zfs.conf
# O número longo representa 16 GB em bytes: 16 * 1024 * 1024 * 1024)

# 2. Atualiza a imagem de boot do sistema para carregar a nova configuração
update-initramfs -u -k all

# 3. Reinicia o servidor para aplicar as alterações em definitivo
reboot
```

---
## Estrutura Final do Servidor
*   **`local` (SAS - ZFS RAID1):** ~270 GB livres para ISOs, Templates LXC e o próprio sistema Proxmox.
*   **`vms-storage` (SATA - ZFS RAID10):** 8 TB líquidos de altíssima velocidade com provisionamento fino (*Thin Provisioning*) para os discos das VMs e Containers.
