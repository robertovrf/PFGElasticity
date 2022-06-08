FROM ubuntu:18.10

COPY ./dana ./home/roberto/dana 
COPY ./PFGElasticity ./home/roberto/PFGElasticity
ENV DANA_HOME=/home/roberto/dana/
ENV PATH="${PATH}:/home/roberto/dana/"
WORKDIR /home/roberto/PFGElasticity/distributor/

ENTRYPOINT ["/bin/bash", "./../scripts/bash/remote_start.sh"]
