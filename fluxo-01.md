# Fluxograma de Instalação e Configuração: Proxmox VE

[ FASE 1: PREPARAÇÃO FÍSICA ]
       │
       ▼
┌────────────────────────────────────────────────────────┐
│ Conectar 2x HD SAS (300GB) + 16x HD SATA (1TB)         │
│ Identificar as controladoras (Deixar em modo IT/JBOD)   │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
[ FASE 2: INSTALAÇÃO DO S.O. ]
                        │
                        ▼
┌────────────────────────────────────────────────────────┐
│ Boot via Pen Drive / ISO Oficial do Proxmox 9.x        │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
┌────────────────────────────────────────────────────────┐
│ Target Harddisk -> Clicar em "Options"                 │
│ Selecionar Filesystem: "zfs (RAID1)"                   │
│ Marcar os 2x HDs SAS de 300GB                          │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
┌────────────────────────────────────────────────────────┐
│ Configurar: País, Fuso Horário, Senha Root, E-mail     │
│ Inserir IP Fixo, Máscara, Gateway e DNS do Servidor    │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
┌────────────────────────────────────────────────────────┐
│ Finalizar Instalação -> Reiniciar o Servidor           │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
[ FASE 3: CONFIGURAÇÃO DO STORAGE PRINCIPAL ]
                        │
                        ▼
┌────────────────────────────────────────────────────────┐
│ Acessar a Interface Web via Navegador (https://IP:8006)│
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
┌────────────────────────────────────────────────────────┐
│ Navegar até: pve-01 -> Disks -> ZFS                     │
│ Clicar em "Create: ZFS"                                │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
┌────────────────────────────────────────────────────────┐
│ Configurar o Pool:                                     │
│ ├─ Name: vms-storage                                   │
│ ├─ RAID Level: RAID10                                  │
│ ├─ ashift: 12                                          │
│ └─ Selecionar os 16x HDs SATA de 1TB                   │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
[ FASE 4: OTIMIZAÇÃO DO SISTEMA (PÓS-INSTALAÇÃO) ]
                        │
                        ▼
┌────────────────────────────────────────────────────────┐
│ Abrir o Shell (Terminal) do Proxmox                    │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
┌────────────────────────────────────────────────────────┐
│ Executar Comando para Limitar RAM do ZFS em 16GB:       │
│ echo "options zfs zfs_arc_max=17179869184" > ...       │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
┌────────────────────────────────────────────────────────┐
│ Atualizar Imagem de Boot:                              │
│ update-initramfs -u -k all                             │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
┌────────────────────────────────────────────────────────┐
│ Reiniciar o Servidor (reboot)                          │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼
[ STATUS FINAL: PRONTO PARA CRIAR VMS E CONTAINERS ]

# Resumo das Ações por Fase
## Fase 1 (Física): 
Garanta que sua placa controladora de discos não está criando RAIDs por hardware (como RAID 0, 5 ou 10 da própria placa). O ZFS precisa de acesso direto aos discos físicos (Modo IT ou HBA) para gerenciar a saúde deles corretamente.

## Fase 2 (S.O.): 
A ISO vai automatizar a criação do sistema operacional espelhado nos discos rápidos SAS corporativos.

## Fase 3 (Interface Web): 
A interface cria o arranjo RAID10 juntando os 16 discos em 8 pares espelhados, resultando em 8 TB líquidos de alta velocidade.

## Fase 4 (Otimização): 
O limite de RAM impede que o Proxmox sofra com falta de memória para as máquinas virtuais devido ao cache do ZFS.
