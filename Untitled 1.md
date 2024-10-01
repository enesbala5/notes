http://www.merre.al:4003, localhost:4003

http://api.merre.al:6003, localhost:6003

cd misc/bin && ./deploy.sh prod build

cd misc/bin && ./deploy.sh prod up -d

> Post Deployment:

pnpx prisma db push
pazar-backend-prod-c