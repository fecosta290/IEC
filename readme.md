# Sistema de Monitoramento com Prometheus

Este projeto configura um sistema de monitoramento utilizando o **Prometheus** e regras de alertas específicas para garantir a saúde e o desempenho adequado da infraestrutura do servidor. As métricas monitoradas incluem CPU, carga do sistema, memória, swap e espaço em disco.

## Visão Geral

O **Prometheus** é uma ferramenta de monitoramento e alerta open-source que coleta e armazena métricas em tempo real. Ele é amplamente utilizado para monitorar servidores, containers, e outras infraestruturas em tempo real. O Prometheus coleta dados de suas instâncias e, com base em regras configuradas, envia alertas para que os administradores de sistema possam agir proativamente.

### Componentes principais do sistema:
- **Prometheus**: Coleta e armazena as métricas.
- **Alertmanager**: Gerencia os alertas e os envia para canais como e-mail, Slack ou outras plataformas.
- **Node Exporter**: Exporta métricas de sistemas operacionais, como CPU, memória, disco e rede.

## Como Funciona o Monitoramento

### Instalação e Configuração:

1. O **Prometheus** é configurado para coletar métricas de sistemas utilizando o **Node Exporter**.
2. As métricas são extraídas em intervalos definidos e armazenadas no **Prometheus**.
3. As regras de alerta são configuradas para disparar notificações quando certos thresholds (limites) são atingidos.

### Regras de Alerta:

- **Alertas de CPU**: Disparam quando o uso da CPU ultrapassa o limite de 90% por um tempo contínuo.
- **Alertas de Carga do Sistema**: Disparam quando a carga do sistema (número de processos que estão esperando para serem executados) atinge um nível insustentável.
- **Alertas de Memória**: Disparam quando a utilização da memória atinge ou ultrapassa 90%.
- **Alertas de Swap**: Disparam quando o uso de swap se torna excessivo, indicando que o sistema está ficando sem memória física.
- **Alertas de Espaço em Disco**: Disparam quando o uso do disco atinge um limite crítico, o que pode levar a falhas no sistema.

## Métricas Monitoradas

### 1. CPU
- **Métrica**: 
    ```prometheus
    avg(rate(node_cpu_seconds_total{mode="user"}[1m])) * 100
    ```
- **Descrição**: Monitora o tempo de CPU usado em modo "usuário", em relação ao tempo total de CPU disponível.
- **Thresholds**:
    - Safe: `avg(rate(node_cpu_seconds_total{mode="user"}[1m])) * 100 < 75`
    - Warning: `avg(rate(node_cpu_seconds_total{mode="user"}[1m])) * 100 >= 75 and < 90`
    - Alert: `avg(rate(node_cpu_seconds_total{mode="user"}[1m])) * 100 >= 90`
  
### 2. Carga do Sistema
- **Métrica**:
    - `node_load1`: Carga média do sistema nos últimos 1 minuto.
    - `node_load5`: Carga média do sistema nos últimos 5 minutos.
    - `node_load15`: Carga média do sistema nos últimos 15 minutos.
- **Thresholds**:
    - Safe: `node_load1 < 1 and node_load5 < 5 and node_load15 < 15`
    - Warning: `node_load1 >= 1 and node_load1 < 2`
    - Alert: `node_load1 >= 2 or node_load5 >= 10 or node_load15 >= 20`

### 3. Memória
- **Métrica**:
    ```prometheus
    (node_memory_MemTotal_bytes - node_memory_MemFree_bytes) / node_memory_MemTotal_bytes * 100
    ```
- **Descrição**: Mede a porcentagem de memória usada no sistema.
- **Thresholds**:
    - Safe: `< 75%`
    - Warning: `>= 75% and < 90%`
    - Alert: `>= 90%`

### 4. Swap
- **Métrica**:
    ```prometheus
    (node_memory_SwapTotal_bytes - node_memory_SwapFree_bytes) / node_memory_SwapTotal_bytes * 100
    ```
- **Descrição**: Mede a porcentagem de swap utilizada.
- **Thresholds**:
    - Safe: `< 25%`
    - Warning: `>= 25% and < 50%`
    - Alert: `>= 50%`

### 5. Espaço em Disco (Root)
- **Métrica**:
    ```prometheus
    (node_filesystem_size_bytes{mountpoint="/"} - node_filesystem_free_bytes{mountpoint="/"}) / node_filesystem_size_bytes{mountpoint="/"} * 100
    ```
- **Descrição**: Mede a porcentagem de uso do espaço em disco da partição root (/).
- **Thresholds**:
    - Safe: `< 75%`
    - Warning: `>= 75% and < 85%`
    - Alert: `>= 85%`

## Como Visualizar os Alertas no Prometheus

1. **Acesse a interface web do Prometheus**:
    - Abra o navegador e digite `http://<ip_do_servidor>:9090`.
    - Clique na aba **"Alerts"** para visualizar os alertas ativos.

2. **Alertmanager**:
    - Caso esteja configurado, o **Alertmanager** irá enviar notificações quando os alertas forem disparados. 
    - Você pode configurá-lo para enviar mensagens via e-mail, Slack, ou outras plataformas de notificação.

## Conclusão

A configuração de monitoramento com o **Prometheus** e as regras de alertas descritas são essenciais para garantir a saúde da infraestrutura de servidores. Monitorar o uso de CPU, memória, swap, carga do sistema e espaço em disco ajuda a identificar e resolver problemas antes que eles afetem a operação do sistema.

Se você estiver utilizando o **Alertmanager** corretamente, será possível receber notificações e agir de forma proativa para resolver os problemas. O objetivo do monitoramento é garantir que o sistema esteja funcionando de forma eficiente e que os administradores possam ser alertados sobre possíveis falhas antes que elas ocorram.
