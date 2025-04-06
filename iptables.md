# iptables

## Популярные команды

- `iptables -L -nv` посмотреть текущие правила
- `iptables -A INPUT -p tcp --dport 22 -j ACCEPT` открыть порт 22
- `iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT` 
