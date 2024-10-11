http://www.merre.al:4003, localhost:4003

http://api.merre.al:6003, localhost:6003

cd misc/bin && ./deploy.sh prod build

cd misc/bin && ./deploy.sh prod up -d

> Post Deployment:

pnpx prisma db push
pazar-backend-prod-c

---

CLOUDINARY_API_SECRET=hxFR3zJSUkjRTouifM7vXoCu4nw
CURRENCY_API_KEY=6692c38c3ce5f8ad102c7a40-25dbcc10c0db
DATABASE_URL=mysql://root:J4BmSjzRXCbFvc@database:3306/main
NODE_ENV=production
NODE_OPTIONS=--max_old_space_size=8192
PORT=4173
PUBLIC_BACKEND_URL=https://api.merre.com
PUBLIC_CLOUDINARY_API_KEY=456488463241268
PUBLIC_CLOUDINARY_CLOUD_NAME=pazar-al
PUBLIC_PAGE_URL=https://merre.al
RESEND_API_KEY=re_ZvyNwtff_Ao8WkWo574s4RpAKmpsvPxAP
VITE_PUBLIC_CLOUDINARY_API_KEY=456488463241268
VITE_PUBLIC_CLOUDINARY_CLOUD_NAME=pazar-al