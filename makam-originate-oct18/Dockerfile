FROM ablu/ubuntu-texlive-full
RUN apt-get update && apt-get install -y wget && wget https://github.com/jgm/pandoc/releases/download/2.3/pandoc-2.3-1-amd64.deb && dpkg -i pandoc-2.3-1-amd64.deb && apt-get install -y python-pandocfilters python-pip wamerican npm wget git
RUN npm install -g n && n 10.6.0
RUN npm install -g yarn
RUN git clone https://github.com/astampoulis/makam && cd makam && git checkout 3836def77b3de0db6c6c222eaaffe5351bdb1c53 && cd webservice && yarn
RUN cd makam/webservice && yarn add makam@0.7.14 serverless-offline@3.25.12 && sed -i -e '/^plugins/ a\ \ - serverless-offline' -e '/^custom/ a\ \ serverless-offline:\n\ \ \ \ host: 0.0.0.0' serverless.yml
COPY ./offline-handler.js makam/webservice/handler.js
CMD bash -c "python -m SimpleHTTPServer & (cd /makam/webservice/ && ./node_modules/.bin/serverless offline);"
