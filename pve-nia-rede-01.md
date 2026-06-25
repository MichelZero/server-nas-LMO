# Planejamento Estratégico de Rede: Proxmox VE (6 Portas Gigabit)
**Status do Hardware:** 2x Portas Integradas (Placa-Mãe) | 4x Portas Dedicadas (Placa de Expansão)

---

## 1. Arquitetura de Rede Proposta
A estratégia consiste na separação física e lógica do tráfego do servidor. O objetivo é isolar a rede de **Gerência/Backups** da rede de **Produção das Máquinas Virtuais (VMs)**, garantindo segurança contra ataques, prevenção de gargalos e alta disponibilidade.

```text
                        ┌──────────────────────────────┐
                        │      SERVIDOR PROXMOX        │
                        └──────────────┬───────────────┘
                                       │
               ┌───────────────────────┴───────────────────────┐
               ▼                                               ▼
     [ Placa-Mãe Onboard ]                           [ Placa de Expansão ]
      Porta 1  +  Porta 2                             Porta 3 + 4 + 5 + 6
               │                                               │
               ▼                                               ▼
       Linux Bond: bond0                               Linux Bond: bond1
      (Modo: Active-Backup)                            (Modo: LACP ou ALB)
               │                                               │
               ▼                                               ▼
      Linux Bridge: vmbr0                             Linux Bridge: vmbr1
         (Com IP Fixo)                               (Sem IP - Modo Trunk)
               │                                               │
               ▼                                               ▼
  ► Interface Web Proxmox (GUI)                   ► Tráfego de Rede das VMs
  ► Acesso SSH / Console                         ► Comunicação dos Serviços
  ► Tráfego de Backup (NAS)
```

---

## 2. Divisão Lógica das Interfaces

### Grupo A: Gerência, Acesso e Backups (`vmbr0`)
Destinado exclusivamente à administração do host Proxmox e tráfego pesado de replicação de dados para o NAS `mycloud-bk-01`.

*   **Portas Físicas:** 2x Portas da Placa-Mãe.
*   **Agrupamento:** `bond0` configurado em modo **Active-Backup** (Ativo-Passivo).
*   **Vantagem:** Não requer configuração no Switch. Se o cabo principal falhar, o secundário assume em menos de 1 segundo.
*   **Configuração de IP:** Recebe o IP estático do Proxmox VE (ex: `192.168.1.100/24`).

### Grupo B: Rede de Produção das VMs e Containers (`vmbr1`)
Destinado a escoar todo o tráfego de dados gerado pelas aplicações virtuais hospedadas no servidor.

*   **Portas Físicas:** 4x Portas da Placa de Expansão PCI-e.
*   **Agrupamento:** `bond1` configurado em modo **LACP (802.3ad)** ou **Balance-ALB**.
*   **Vantagem:** Permite agregar a largura de banda. As VMs dividirão dinamicamente um barramento total de até 4 Gbps.
*   **Configuração de IP:** **Sem IP atribuído no Proxmox.** Funciona puramente como um Switch Virtual (as VMs pegam IP direto do seu roteador/DHCP da rede).

---

## 3. Passo a Passo de Configuração via Interface Web

Acesse o painel do Proxmox e navegue até: **pve-01** > **System** > **Network**.

### Passo 3.1: Configurar a Rede das VMs (Placa de 4 Portas)
1. Clique em **Create** > **Linux Bond**.
2. Preencha os campos:
   *   **Name:** `bond1`
   *   **Slaves:** Digite os nomes das 4 interfaces da placa de expansão separados por espaço *(Exemplo: `enp5s0 enp6s0 enp7s0 enp8s0`)*.
   *   **Mode:** Escolha `LACP (802.3ad)` se o seu switch for gerenciável, ou `balance-alb` se o seu switch for comum/caseiro.
3. Clique em **Create**.
4. Clique em **Create** > **Linux Bridge**.
5. Preencha os campos:
   *   **Name:** `vmbr1`
   *   **Bridge ports:** Digite `bond1`
   *   Deixe os campos de IP e Gateway em **branco**.
6. Clique em **Create**.

### Passo 3.2: Configurar a Rede de Gerência (2 Portas Integradas)
*Nota: A instalação padrão já cria a `vmbr0` na primeira porta. Vamos adicionar a segunda porta para redundância.*

1. Selecione a `vmbr0` existente e clique em **Edit**.
2. **Copie** o IP, Máscara e Gateway anotados nela. Em seguida, **exclua** a `vmbr0` (não se preocupe, as alterações só entram em vigor ao aplicar).
3. Clique em **Create** > **Linux Bond**.
4. Preencha os campos:
   *   **Name:** `bond0`
   *   **Slaves:** Digite as 2 interfaces da placa-mãe *(Exemplo: `16035_enp3s0 enp4s0`)*.
   *   **Mode:** Escolha `active-backup`.
5. Clique em **Create**.
6. Clique em **Create** > **Linux Bridge**.
7. Preencha os campos:
   *   **Name:** `vmbr0`
   *   **IPv4/CIDR:** Insira o IP que você copiou anteriormente.
   *   **Gateway:** Insira o Gateway que você copiou.
   *   **Bridge ports:** Digite `bond0`.
8. Clique em **Create**.

### Passo 3.3: Ativar as Alterações
*   Na barra superior da tela de redes, clique no botão **Apply Configuration**. O Proxmox reconfigurará o sistema de rede em tempo real sem derrubar o servidor.

---

## 4. Como Vincular as VMs à Nova Rede
Ao criar ou editar qualquer Máquina Virtual ou Container (LXC):
1. Vá na aba **Network** da VM.
2. No campo **Bridge**, altere de `vmbr0` para **`vmbr1`**.

As VMs estarão navegando pela placa de 4 portas, enquanto a gerência e os backups para o NAS acontecem de forma isolada e segura pela placa-mãe.
