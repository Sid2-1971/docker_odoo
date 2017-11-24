FROM ubuntu:16.04
MAINTAINER Sidnei Brianti <scbrianti@gmailcom>

# Exporta usuarios
ENV USER odoo
ENV PASSWORD odoo
ENV DEBIAN_FRONTEND noninteractive

# Instala pacotes basicos
ADD apt_prerequisito_odoo.pgks /tmp/apt_prerequisito_odoo.pgks
RUN apt-get -qq update
RUN apt-get -qq dist-upgrade
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y -q $(cat /tmp/apt_prerequisito_odoo.pgks)

# Configura localizacao
RUN locale-gen pt_BR.UTF-8
RUN update-locale LANG=pt_BR.UTF-8

# Install ntp e configura localizacao
RUN echo "server a.ntp.br" >> /etc/ntp.conf
RUN echo "server b.ntp.br" >> /etc/ntp.conf
RUN echo "server c.ntp.br" >> /etc/ntp.conf

RUN ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
RUN echo "America/Sao_Paulo" > /etc/timezone
RUN dpkg-reconfigure -f noninteractive tzdata

# Configura ssh
RUN mkdir /var/run/sshd
RUN echo 'root:12345678' |chpasswd
RUN sed -i "s/PermitRootLogin without-password/PermitRootLogin yes/g" /etc/ssh/sshd_config
RUN echo "HISTTIMEFORMAT='|  $USER  |  %F  |  %H:%M:%S  |  '" >> /root/.bashrc


# Adiciona user e baixa repositorios
RUN adduser --system --home=/opt/odoo --group odoo

USER odoo
RUN mkdir /opt/odoo/addons
#RUN mkdir /opt/odoo/addons/outros
#RUN mkdir /opt/odoo/etc
#RUN mkdir /opt/odoo/local
#RUN mkdir /opt/odoo/local/sped
#ADD odoo8.cfg /opt/odoo/etc/odoo8.cfg

WORKDIR /opt/odoo/
RUN git clone https://github.com/OCA/OCB.git --depth 1 --branch 10.0 --single-branch server

WORKDIR /opt/odoo/addons
RUN git clone https://github.com/OCA/server-tools.git --depth 1 --branch 10.0
RUN git clone https://github.com/OCA/account-fiscal-rule.git --depth 1 --branch 10.0
RUN git clone https://github.com/OCA/reporting-engine.git --depth 1 --branch 10.0
RUN git clone https://github.com/OCA/l10n-brazil.git --depth 1 --branch 10.0
RUN git clone https://github.com/Openworx/backend_theme.git --depth 1 --branch 10.0
RUN git clone https://github.com/OCA/web.git --depth 1 --branch 10.0

# Instala pips requerimentos
USER root
WORKDIR /tmp
ADD requirements.txt /tmp/requirements.txt
ADD wkhtmltox-0.12.1_linux-trusty-amd64.deb /tmp/wkhtmltox-0.12.1_linux-trusty-amd64.deb
RUN pip install -r /opt/odoo/server/requirements.txt
RUN pip install -r /opt/odoo/addons/reporting-engine/requirements.txt
RUN pip install -r /opt/odoo/addons/l10n-brazil/requirements.txt
RUN dpkg -i /tmp/wkhtmltox-0.12.1_linux-trusty-amd64.deb
RUN cp /usr/local/bin/wkhtmltopdf /usr/bin
RUN cp /usr/local/bin/wkhtmltoimage /usr/bin
RUN ln -s /usr/bin/nodejs /usr/bin/node
RUN npm install -g less less-plugin-clean-css


# Install systemd script
#ADD systemd-odoo.conf /etc/init.d/odoo
#RUN update-rc.d -f odoo defaults

# Instalar NGinx
#RUN DEBIAN_FRONTEND=noninteractive apt-get install -y -q nginx
#ADD nginx-odoo /etc/nginx/sites-enabled/nginx-odoo
#RUN rm -rf /etc/nginx/sites-enabled/default

# Install supervisor
#RUN DEBIAN_FRONTEND=noninteractive apt-get install -y -q supervisor
#ADD supervisord.conf /etc/supervisor/conf.d/

# Configura volume e port
VOLUME ["/opt/odoo"]
EXPOSE 8069

#CMD ["/usr/bin/supervisord"]
