# VLAN-Lab-Cisco-Devops / Laboratório: Configuração de VLANs com Router-on-a-Stick
🔧 Laboratório completo de redes: VLANs + Router-on-a-Stick + DHCP \\ 🎓 Santander Imersão Digital | Alura | FIAP | Prof. Olivia Braga

## 📋 Descrição
Implementação prática de uma rede segmentada com VLANs usando Cisco Packet Tracer. Este laboratório demonstra a configuração de VLANs, roteamento inter-VLAN via Router-on-a-Stick e servidores DHCP dedicados por segmento.

## 🎯 Objetivos
- Configurar VLANs em switch Cisco 2950-24
- Implementar roteamento entre VLANs usando Router-on-a-Stick
- Configurar servidores DHCP específicos para cada VLAN
- Validar comunicação e isolamento entre segmentos de rede

## 🎬 Vídeo Demonstrativo
📺 **Demonstração completa do laboratório:**
[Assista no YouTube](https://www.youtube.com/watch?v=vZcc4f3Smz0)

# 📁 Topologia da Rede

| Componente | Especificação |
|------------|---------------|
| **Roteador** | Cisco 2811 |
| **Interface do Roteador** | FastEthernet0/0 |
| **Subinterfaces** | .10, .20, .30 |
| **Switch** | Cisco 2950-24 |
| **Porta Trunk** | FastEthernet0/24 |
| **VLAN 10 (RH)** | Portas 1-5 |
| **VLAN 20 (Engenharia)** | Portas 6-10 |
| **VLAN 30 (Financeiro)** | Portas 11-15 |

## 📦 Arquivos do Projeto

| Arquivo | Descrição | Link |
|---------|-----------|------|
| `Configuraçao_Vlan_Dot1Q.pkt` | Topologia completa do laboratório | [Baixar](Configuraçao_Vlan_Dot1Q.pkt) |
| `README.md` | Documentação completa do projeto | [Ver](README.md) |

## 🚀 Como Usar o Arquivo .pkt

1. **Baixe o arquivo** `Configuraçao_Vlan_Dot1Q.pkt`
2. **Abra no Cisco Packet Tracer** (versão 7.0 ou superior)
3. **A topologia já estará montada** com:
   - Switch 2950-24 configurado
   - Roteador 2811 com subinterfaces
   - VLANs definidas
   - Servidores DHCP configurados
   - 
## 🛠️ Configurações Completas

### 📍 Configuração do Roteador 2811

```cisco
! Ativar interface principal
Router>enable
Router#configure terminal
Router(config)#interface f0/0
Router(config-if)#no shut
Router(config-if)#end

! Criar subinterfaces para cada VLAN
Router#configure terminal
Router(config)#int f0/0.10
Router(config-subif)#encapsulation dot1Q 10
Router(config-subif)#ip address 192.168.1.1 255.255.255.0

Router(config-subif)#int f0/0.20
Router(config-subif)#encapsulation dot1Q 20
Router(config-subif)#ip add 192.168.2.1 255.255.255.0

Router(config-subif)#int f0/0.30
Router(config-subif)#encapsulation dot1Q 30
Router(config-subif)#ip address 192.168.3.1 255.255.255.0
Router(config-subif)#end
Router#wr

! Configurar servidores DHCP
Router#configure terminal
Router(config)#ip dhcp excluded-address 192.168.1.1
Router(config)#ip dhcp excluded-address 192.168.2.1
Router(config)#ip dhcp excluded-address 192.168.3.1

Router(config)#ip dhcp pool LAN1
Router(dhcp-config)#network 192.168.1.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.1.1

Router(dhcp-config)#ip dhcp pool LAN2
Router(dhcp-config)#network 192.168.2.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.2.1

Router(dhcp-config)#ip dhcp pool LAN3
Router(dhcp-config)#network 192.168.3.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.3.1
Router(dhcp-config)#end

! Finalizar e salvar
Router#configure terminal
Router#interface f0/0
Router(config-inf)#no shutdown
Router(config-inf)#end
Router#wr
📍 Configuração do Switch 2950-24
cisco
! Criar VLANs
Switch>enable
Switch#configure terminal
Switch(config)#vlan 10
Switch(config)#name RH
Switch(config-vlan)#vlan 20
Switch(config)#name Engenharia
Switch(config-vlan)#vlan 30
Switch(config)#name financeiro
Switch(config)#exit
Switch#write

! Atribuir portas às VLANs
Switch#configure terminal
Switch(config)#vlan 10
Switch(config-vlan)#int range f0/1 - 5
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport access vlan 10

Switch(config-if-range)#int range f0/6-10
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport access vlan 20

Switch(config-if-range)#int range f0/11 - 15
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport access vlan 30

Switch(config-if-range)#end
Switch#do write

! Configurar porta trunk
Switch#configure terminal
Switch(config)#interface f0/24
Switch(config-if)#switchport mode trunk
Switch(config-if)#end
Switch#write
📊 Esquema de Endereçamento
VLAN	Departamento	Gateway	Rede	Faixa de IPs
10	RH	192.168.1.1	192.168.1.0/24	192.168.1.2-254
20	Engenharia	192.168.2.1	192.168.2.0/24	192.168.2.2-254
30	Financeiro	192.168.3.1	192.168.3.0/24	192.168.3.2-254
🔍 Comandos de Verificação
cisco
! No Switch
show vlan brief
show interfaces trunk
show interfaces status
show mac address-table
show running-config

! No Roteador
show ip interface brief
show ip dhcp binding
show ip route
show running-config
ping 192.168.1.1
🧪 Testes Recomendados
Teste de DHCP:

Conectar PC em cada VLAN

Executar ipconfig /renew (Windows) ou dhclient (Linux)

Verificar se obteve IP da VLAN correta

Teste de Conectividade Intra-VLAN:

Ping do PC para seu gateway (deve funcionar)

Ping entre PCs da mesma VLAN (deve funcionar)

Teste de Isolamento Inter-VLAN:

Ping de PC na VLAN 10 para PC na VLAN 20 (deve falhar sem roteamento adicional)

📈 Benefícios da Implementação
✅ Segurança Aprimorada - Isolamento entre departamentos
✅ Performance Otimizada - Domínios de broadcast menores
✅ Gestão Simplificada - Agrupamento lógico de dispositivos
✅ Flexibilidade - Facilidade na realocação de recursos
✅ Custo Reduzido - Uso eficiente da infraestrutura física

🚀 Como Executar
Requisitos:

Cisco Packet Tracer 7.0+

Conhecimento básico de linha de comando Cisco (CLI)

Passos:

Baixe o arquivo .pkt deste repositório

Abra no Cisco Packet Tracer

Aplique as configurações acima

Execute os testes de validação

📚 Contexto Educacional
Este laboratório foi desenvolvido como parte do programa Santander Imersão Digital - Trilha DevOps, em parceria com Alura e FIAP, sob orientação da professora Olivia Ramos Braga.

👤 Autor
Professora Olivia Ramos Braga

Executado por Amelia Viana Pereira de Andrade
Estudante de DevOps - Santander Imersão Digital
www.linkedin.com/in/devviana-amelia

📄 Licença
Este projeto é para fins educacionais. Sinta-se à vontade para usar e modificar conforme necessário para seus estudos.
