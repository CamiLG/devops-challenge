FROM python:3.9.17-slim

#Copy project to the container folder called app
COPY . /app

#Define the folder to work with
WORKDIR /app

#Performing some installations for database and other packages needed
RUN apt-get update \
    && apt-get install -y postgresql \
    && apt-get install -y gcc \
    && apt-get install -y zlib1g-dev \
    && apt-get install -y graphviz \
    && apt-get install -y pkg-config \
    && apt-get install -y libjpeg-dev \
    && apt-get install -y libgraphviz-dev \
    && apt-get install -y libpq-dev

RUN pip install setuptools==58 \
    && pip install -r requirements.txt

EXPOSE 8000

CMD [ "python", "manage.py", "runserver", "0.0.0.0:8000" ]

