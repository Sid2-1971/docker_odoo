FROM ubuntu:16.04
MAINTAINER Sidnei Brianti <scbrianti@gmailcom>

# Exporta usuarios
ENV USER odoo
ENV PASSWORD odoo

# Instala pacotes basicos
ADD apt_prerequisito_odoo.pgks /tmp/apt_prerequisito_odoo.pgks
RUN apt-get -qq update \
    && apt-get install -y -q $(cat /tmp/apt_prerequisito_odoo.pgks) \
    && apt-get clean

# Configura localizacao
RUN locale-gen pt_BR.UTF-8 \
    && update-locale LANG=pt_BR.UTF-8

# Configura localizacao
RUN echo "server a.ntp.br" >> /etc/ntp.conf \
    && echo "server b.ntp.br" >> /etc/ntp.conf \
    && echo "server c.ntp.br" >> /etc/ntp.conf

RUN ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime \
    && echo "America/Sao_Paulo" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata

# Configura ssh
RUN mkdir /var/run/sshd
RUN echo 'root:12345678' |chpasswd \
    && sed -i "s/PermitRootLogin without-password/PermitRootLogin yes/g" /etc/ssh/sshd_config \
    && echo "HISTTIMEFORMAT='|  $USER  |  %F  |  %H:%M:%S  |  '" >> /root/.bashrc


# Adiciona user "odoo" e diretórios
RUN adduser --system --home=/opt/odoo --group odoo

USER odoo
RUN mkdir /opt/odoo/addons
#RUN mkdir /opt/odoo/addons/outros
#RUN mkdir /opt/odoo/etc
#RUN mkdir /opt/odoo/local
#RUN mkdir /opt/odoo/local/sped
#ADD odoo8.cfg /opt/odoo/etc/odoo8.cfg

# Adiciona os repositórios
WORKDIR /opt/odoo/
RUN git clone https://github.com/OCA/OCB.git --depth 1 --branch 10.0 --single-branch server

WORKDIR /opt/odoo/addons
RUN git clone https://github.com/OCA/server-tools.git --depth 1 --branch 10.0 \
    && git clone https://github.com/OCA/account-fiscal-rule.git --depth 1 --branch 10.0 \
    && git clone https://github.com/OCA/reporting-engine.git --depth 1 --branch 10.0 \
    && git clone https://github.com/OCA/l10n-brazil.git --depth 1 --branch 10.0 \
    && git clone https://github.com/Openworx/backend_theme.git --depth 1 --branch 10.0 \
    && git clone https://github.com/OCA/web.git --depth 1 --branch 10.0

# Instala pips requerimentos
USER root
WORKDIR /tmp
ADD requirements.txt /tmp/requirements.txt
ADD wkhtmltox-0.12.1_linux-trusty-amd64.deb /tmp/wkhtmltox-0.12.1_linux-trusty-amd64.deb
RUN pip install --upgrade pip \
    && pip install -r /opt/odoo/server/requirements.txt \
    && pip install -r /opt/odoo/addons/reporting-engine/requirements.txt \
    && pip install -r /opt/odoo/addons/l10n-brazil/requirements.txt \
    && dpkg -i /tmp/wkhtmltox-0.12.1_linux-trusty-amd64.deb \
    && cp /usr/local/bin/wkhtmltopdf /usr/bin \
    && cp /usr/local/bin/wkhtmltoimage /usr/bin \
    && ln -s /usr/bin/nodejs /usr/bin/node \
    && npm install -g less less-plugin-clean-css \
    && rm /tmp/*


# Install systemd script
#ADD systemd-odoo.conf /etc/init.d/odoo
#RUN update-rc.d -f odoo defaults

# Configura volume e port
VOLUME ["/opt/odoo"]
EXPOSE 8069

#CMD ["/usr/bin/supervisord"]
