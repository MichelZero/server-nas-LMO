# Server NAS - LMO (Proxmox VE & Network Attached Storage)

Este repositório contém a documentação, diagramas de fluxo e guias de configuração para a implementação de um servidor **NAS (Network Attached Storage)** centralizado, gerenciado através do hipervisor **Proxmox VE (PVE)**.

O objetivo deste projeto é documentar a arquitetura de rede, fluxos de armazenamento, automações e provisionamento do laboratório/servidor LMO.

---

## 📂 Estrutura do Repositório

O projeto está organizado em guias específicos de configuração e fluxos operacionais:

*   **`fluxo-01.md` / `fluxo-02.md`**: Detalhamento dos fluxos de dados, rotinas de backup, sincronização e permissões de usuários.
*   **`pve-nia-01.md`**: Guia de provisionamento do nó Proxmox VE, incluindo configurações iniciais de hardware e armazenamento.
*   **`pve-nia-rede-01.md`**: Documentação da topologia de rede, criação de pontes (bridges), VLANs e isolamento de tráfego do servidor.

---

## 🛠️ Tecnologias e Ferramentas

*   **Proxmox VE (PVE)**: Plataforma de virtualização para gerenciamento de LXCs (Containers) e VMs.
*   **Sistemas de Arquivos/NAS**: (Ex: TrueNAS, OpenMediaVault ou Samba/NFS nativo) para compartilhamento de arquivos.
*   **Protocolos de Rede**: SMB/CIFS, NFS, SSH, e mapeamentos de rede locais.

---

## 🚀 Como Utilizar Este Repositório

1. **Planejamento de Rede**: Comece revisando o arquivo `pve-nia-rede-01.md` para entender o escopo de IPs e conexões.
2. **Instalação do Hipervisor**: Siga os passos descritos em `pve-nia-01.md` para preparar o ambiente Proxmox.
3. **Configuração de Fluxos**: Aplique as lógicas de armazenamento e transferência documentadas nos arquivos `fluxo-*.md`.

---

## 📄 Licença

Este projeto está sob a licença MIT. Consulte o arquivo [LICENSE](LICENSE) para obter mais detalhes.
