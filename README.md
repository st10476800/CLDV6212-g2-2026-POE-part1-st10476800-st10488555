# CLDV6212-g2-2026-POE-part1-st10476800-st10488555
# CoffeeChill – Menu & Staff Document API

This is our Azure Functions app for CoffeeChill. It handles menu items and staff documents through simple HTTP endpoints, and it stores everything in Azurite (Table storage for the menu, Blob/File storage for staff documents) so it can run without a real Azure account.

CLDV6212 Group 2 – 2026 – Portfolio of Evidence - Part 1 - Group 15
Students: st10476800, st10488555

## What you need before you start

- Docker Desktop, up and running
- Postman, to test the endpoints
- .NET SDK and Azure Functions Core Tools, but only if you want to run it outside Docker

## Getting it running

1. Clone the repo:
   ```
   git clone <your-repo-url>
   cd <repo-folder-name>
   ```
2. Open Docker Desktop and make sure it's running.
3. Follow the Docker steps below to start both containers.
4. Once they're both up, the API is live at `http://localhost:7071/api`.
5. Open Postman, import the collection from `/docs`, and set the `baseUrl` variable to `http://localhost:7071/api`.

## Running it with Docker

You don't need to build anything yourself here. Both images are already on Docker Hub, so these commands just pull them down and run them.

First, create a network so the two containers can actually see each other:
```
docker network create coffeenchill-net
```

Then start Azurite:
```
docker run -d --name azurite --network coffeenchill-net -p 10000:10000 -p 10001:10001 -p 10002:10002 st10476800/coffeenchill-azurite:v1.0
```

Then start the Functions app, pointing it at that same Azurite container:
```
docker run -d --name coffeenchill-functions --network coffeenchill-net -p 7071:80 -e AzureWebJobsStorage="DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://azurite:10000/devstoreaccount1;QueueEndpoint=http://azurite:10001/devstoreaccount1;TableEndpoint=http://azurite:10002/devstoreaccount1;" st10476800/coffeenchill-functions:v1.0
```

Give it a few seconds, then open `http://localhost:7071/api/menu` in a browser. On a fresh Azurite container you should just see `[]`, that means it worked.

When you're done and want to clean up:
```
docker rm -f coffeenchill-functions azurite
```

(Quick check before you rely on this: make sure the `AzureWebJobsStorage` value above actually matches what's in your `local.settings.json`. If your app expects a different variable name, swap it in.)

## What each endpoint does

Everything sits under `/api`. The Postman collection in `/docs` has full examples for all of these, this is just the quick reference.

**Menu items**
- `POST /menu` – add a new menu item
- `GET /menu` – get every menu item
- `GET /menu/category/{category}` – get menu items in one category
- `PUT /menu/{id}` – update a menu item
- `DELETE /menu/{id}` – delete a menu item

**Staff documents**
- `POST /staffdocuments` – upload a document
- `GET /staffdocuments` – list all documents
- `GET /staffdocuments/{id}` – download one document

(Double-check these routes against your actual function bindings, they should match, but worth a quick look.)

## How we tested it

We exported our full Postman collection and it's committed at `docs/<collection-name>.json`. To run it yourself: import it, point `baseUrl` at `http://localhost:7071/api`, then run the requests in this order, Create, GetAll, GetByCategory, Update, Delete for the menu, then Upload, List, Download for staff documents.

## Docker Hub

- Functions image: [st10476800/coffeenchill-functions:v1.0](https://hub.docker.com/r/st10476800/coffeenchill-functions)
- Azurite image: [st10476800/coffeenchill-azurite:v1.0](https://hub.docker.com/r/st10476800/coffeenchill-azurite)

## Who worked on what

What they worked on

st10476800: Built the menu item functions (create, get all, get by category, update, delete), set up the Dockerfile and pushed both images to Docker Hub, ran the full Postman test pass 
st10488555 Built the staff document functions (upload, list, download), set up the Azurite Table and Blob storage connections, wrote the README and put together the Postman collection 

## Demo video

*TODO: add YouTube link here*
