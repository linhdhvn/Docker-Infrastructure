[
  {
    "app_name": "erpnext",
    "url": "https://github.com/frappe/erpnext",
    "branch": "version-16"
  },
  {
    "app_name": "hrms",
    "url": "https://github.com/frappe/hrms",
    "branch": "version-16"
  },
  {
    "app_name": "helpdesk",
    "url": "https://github.com/frappe/helpdesk",
    "branch": "main"
  }
]


docker build --no-cache --build-arg=FRAPPE_PATH=https://github.com/frappe/frappe --build-arg=FRAPPE_BRANCH=version-16 --secret=id=apps_json,src=apps.json --tag=frappe/erpnext-custom:v16 --file=images/production/Containerfile .



docker compose --env-file custom.env -f compose.yaml -f overrides/compose.redis.yaml -f overrides/compose.noproxy.yaml config > docker-compose.yaml

docker compose -p frappe -f docker-compose.yaml up -d


docker compose -f docker-compose.yaml exec backend bench setup production frappe

docker compose -f docker-compose.yaml exec backend bench get-app --branch version-16 hrms
docker compose -f docker-compose.yaml exec backend bench --site sala-frontend.local install-app hrms

