FROM centos:7

EXPOSE 80 443

WORKDIR /opt

RUN useradd -s /bin/false pmm
RUN yum -y install epel-release && yum -y install ansible
RUN yum -y update

COPY RPMS /tmp/RPMS
COPY gitCommit /tmp/gitCommit
COPY playbook-install.yml /opt/playbook-install.yml
RUN ansible-playbook -vvv -i 'localhost,' -c local /opt/playbook-install.yml

RUN mkdir -p /opt/consul-data && chown -R pmm:pmm /opt/consul-data && \
    mkdir -p /opt/prometheus/data && chown -R pmm:pmm /opt/prometheus/data && \
    mkdir -p /var/lib/mysql && chown -R mysql:mysql /var/lib/mysql && \
    mkdir -p /var/lib/grafana && chown -R grafana:grafana /var/lib/grafana

COPY playbook-init.yml /opt/playbook-init.yml
RUN ansible-playbook -vvv -i 'localhost,' -c local /opt/playbook-init.yml
RUN cp /usr/share/pmm-server/entrypoint.sh /opt/entrypoint.sh

RUN cp -a /opt/consul-data /opt/consul-data.orig && \
    cp -a /opt/prometheus/data /opt/prometheus/data.orig && \
    cp -a /var/lib/mysql /var/lib/mysql.orig && \
    cp -a /var/lib/grafana /var/lib/grafana.orig

VOLUME /opt/consul-data
VOLUME /opt/prometheus/data
VOLUME /var/lib/mysql
VOLUME /var/lib/grafana

CMD ["/opt/entrypoint.sh"]
