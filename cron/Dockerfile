FROM debian:bookworm-slim

ENV TZ=Europe/Kiev

RUN apt-get -y update \
    && apt-get install -y \
      cron \
      wget \
      git \
      vim \
    && ln -sf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    && git clone https://github.com/florchuk/currency-exchange-rate-scraper.git /tmp/currency-exchange-rate-scraper \
    && wget https://download.oracle.com/java/21/latest/jdk-21_linux-x64_bin.deb -P /tmp \
    && wget https://dlcdn.apache.org/maven/maven-3/3.9.8/binaries/apache-maven-3.9.8-bin.tar.gz -P /tmp \
    && apt-get install -y /tmp/jdk-21_linux-x64_bin.deb \
    && tar -C /tmp -xzvf /tmp/apache-maven-3.9.8-bin.tar.gz \
    && rm /tmp/apache-maven-3.9.8-bin.tar.gz \
        /tmp/jdk-21_linux-x64_bin.deb \
    && mkdir /opt/currency-exchange-rate-scraper

COPY ./cron/config.properties /opt/currency-exchange-rate-scraper
COPY ./cron/root /var/spool/cron/crontabs

RUN /tmp/apache-maven-3.9.8/bin/mvn -f /tmp/currency-exchange-rate-scraper/pom.xml package \
    && mv /tmp/currency-exchange-rate-scraper/target/currency-exchange-rate-scraper.jar /opt/currency-exchange-rate-scraper \
    && mv /tmp/currency-exchange-rate-scraper/target/lib /opt/currency-exchange-rate-scraper \
    && rm -R /tmp/currency-exchange-rate-scraper \
      /tmp/apache-maven-3.9.8 \
    && apt-get remove -y \
      wget \
      git \
    && apt-get autoremove -y \
    && crontab /var/spool/cron/crontabs/root

CMD ["cron", "-f"]