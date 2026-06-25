# Fluxograma do Tráfego de Rede: NIA-VMs/NAS (6 Portas)

Este fluxo ilustra o caminho do dado desde a infraestrutura física (cabos/portas) até a camada lógica de gerência e aplicação (VMs).

```text
              [ INFRAESTRUTURA FÍSICA DO SERVIDOR (6 PORTAS GIGABIT) ]
                                          │
            ┌─────────────────────────────┴─────────────────────────────┐
            ▼                                                           ▼
 ┌─────────────────────┐                                     ┌─────────────────────┐
 │  Portas Onboard     │                                     │ Placa de Expansão   │
 │  (Placa-Mãe)        │                                     │ (PCI-Express)       │
 └──┬───────────────┬──┘                                     └──┬───┬───┬───────┬──┘
    │               │                                           │   │   │       │
    ▼               ▼                                           ▼   ▼   ▼       ▼
 [Porta 1]       [Porta 2]                                   [P3] [P4] [P5]   [P6]
    │               │                                           │   │   │       │
    └───────┬───────┘                                           └───┴───┴───┬───┘
            │                                                               │
            ▼                                                               ▼
  [ FASE 1: AGREGRAÇÃO / BONDING ]                                [ FASE 1: AGREGRAÇÃO / BONDING ]
 ┌──────────────────────────────┐                                ┌──────────────────────────────┐
 │ Linux Bond: bond0            │                                │ Linux Bond: bond1            │
 │ Modo: Active-Backup          │                                │ Modo: LACP ou Balance-ALB    │
 └──────────┬───────────────────┘                                └──────────┬───────────────────┘
            │                                                               │
            ▼                                                               ▼
  [ FASE 2: SWITCH VIRTUAL / BRIDGE ]                             [ FASE 2: SWITCH VIRTUAL / BRIDGE ]
 ┌──────────────────────────────┐                                ┌──────────────────────────────┐
 │ Linux Bridge: vmbr0          │                                │ Linux Bridge: vmbr1          │
 │ Carrega o IP do Proxmox      │                                │ Sem IP (Modo Trunk)          │
 └──────────┬───────────────────┘                                └──────────┬───────────────────┘
            │                                                               │
            ▼                                                               ▼
  [ FASE 3: DESTINO DO TRÁFEGO ]                                  [ FASE 3: DESTINO DO TRÁFEGO ]
 ┌──────────────────────────────┐                                ┌──────────────────────────────┐
 │ ► Painel Web (GUI) HTTPS     │                                │ ► Máquina Virtual (VM) 01    │
 │ ► Acesso Console / SSH       │                                │ ► Máquina Virtual (VM) 02    │
 │ ► Backups para o NAS         │                                │ ► Container LXC 01           │
 └──────────────────────────────┘                                └──────────────────────────────┘
```

### Explicação do Fluxo de Dados

1. **Camada Física:** O sistema operacional detecta duas portas vindas da placa-mãe e quatro portas vindas da placa controladora de rede adicional. Elas operam de forma 100% independente.
2. **Camada de Bonding (Agregação):**
   * O `bond0` une as portas da placa-mãe para que, se um cabo quebrar, o outro assuma sem derrubar o acesso ao servidor.
   * O `bond1` une as 4 portas de expansão para que funcionem juntas, permitindo velocidades combinadas de até 4 Gbps de tráfego agregado.
3. **Camada de Bridge (Pontes Virtuais):**
   * A `vmbr0` recebe a configuração de rede principal do servidor (IP, Máscara e Gateway). É a "cara" do Servidor na rede.
   * A `vmbr1` funciona apenas como um passador de cabos invisível. Ela não possui IP no Proxmox, servindo apenas para conectar as VMs diretamente ao switch físico do seu rack.
