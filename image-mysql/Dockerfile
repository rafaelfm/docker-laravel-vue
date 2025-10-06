FROM mysql:8.0.26
ENV TERM xterm

# As configurações adicionais são salvas na pasta /etc/mysql/conf.d/, sobrescrevendo
# as do arquivo /etc/mysql/my.cnf quando carregadas
COPY assets/my.cnf /etc/mysql/conf.d/my.cnf