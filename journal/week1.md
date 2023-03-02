# Week 1 — App Containerization

## Required Homework

## Containerize Backend
Create a [Dockerfile](https://github.com/aravindmenon1197/aws-bootcamp-cruddur-2023/blob/main/backend-flask/Dockerfile) for backend of Cruddur app.

<img src="image/Docker1.JPG" alt="docker1" width="75%" height="75%" />

### To run the app on localhost
- Install the required python libraries

<img src="image/Requirements.JPG" alt="req" width="75%" height="75%" />

- Running the flask app  ``` python3 -m flask run --host=0.0.0.0 --port=4567 ```

<img src="image/Port1.JPG" alt="port1" width="75%" height="75%" />


### Check for available ports.
- Unlock the required ports. In this case, it is **port 4567 for backend**.
- Open the address for **port 4567**.

<img src="image/Port2.JPG" alt="port2" width="75%" height="75%" />

- The server is running and accepting requests but it returns **Error 404**

<img src="image/Port3.JPG" alt="port3" width="75%" height="75%" />

To correct **Error 404**, we have to set environment variables **FRONTEND_URL** and **BACKEND_URL**
```
export FRONTEND_URL="*"
export BACKEND_URL ="*"
```
- Start the server again and open the address for **port 4567** 
- Append the endpoint **``` /api/activities/home ```** to the url.

<img src="image/Port4.JPG" alt="port4" width="75%" height="75%" />

- We are able to see the json data in the required address.

<img src="image/localrun.JPG" alt="localrun" width="75%" height="75%" />

- Unset the environment variables.
```
unset FRONTEND_URL
unset BACKEND_URL
```
## Build Container

- Change to the main directory
- To build the docker image, run the command **``` docker build -t  backend-flask ./backend-flask ```**

<img src="image/build1.JPG" alt="build1" width="75%" height="75%" />

- View the docker image using ``` docker images``` 

<img src="image/dockerimage.JPG" alt="dockerimage" width="75%" height="75%" />

## Run Container

- To run the image, we use the command ``` docker run --rm -p 4567:4567 -it backend-flask ```

<img src="image/dockerun.JPG" alt="dockerun" width="75%" height="75%" />

- Check for available ports.
- Unlock the required ports. In this case, it is **port 4567**.
- Open the address for **port 4567**.
- The server is running and accepting requests but it returns **Error 404** because whe have not set environment variables **FRONTEND_URL** and **BACKEND_URL**.

### To set environment variables
- Attach the shell 

<img src="image/attachshell.JPG" alt="attachshell" width="75%" height="75%" />

- Check if the environment variables using the **env** command
- Run the image again
```
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
```
- Open the address for **port 4567**  and append the endpoint **``` /api/activities/home ```** to the url.
- We are able to see the json data in the required address.

<img src="image/localrun1.JPG" alt="localrun1" width="75%" height="75%" />

### To run the image in background, use the command 
```
docker container run --rm -p 4567:4567 -d backend-flask
```

### To view the running containers, use the command ```**docker ps**```

<img src="image/dockerps.JPG" alt="dockerps" width="75%" height="75%" />


## Containerize Frontend 
We have to run npm install 
```
cd frontend-react-js
npm i
```

Create a [Dockerfile](https://github.com/aravindmenon1197/aws-bootcamp-cruddur-2023/blob/main/frontend-react-js/Dockerfile) for backend of Cruddur app.

```
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```

## Create **docker-compose.yml** in the root directory
- Compose helps to run multiple containers.
```
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

networks: 
  internal-network:
    driver: bridge
    name: cruddur

```
### Start the app either by running ```docker compose -f "docker-compose.yml" up -d --build```  
**OR**

/workspace/aws-bootcamp-cruddur-2023/docker-compose.yml

<img src="image/composeup.JPG" alt="composeup" width="75%" height="75%" />



<img src="image/dockercompose1.JPG" alt="dockercompose1" width="75%" height="75%" />

### Check for available ports.
- Unlock the required ports. In this case, it is **port 3000 for frontend**.
- Open the address for **port 4567**.

<img src="image/Port6.JPG" alt="Port6" width="75%" height="75%" />

### We have launched the app successfully

<img src="image/frontend.JPG" alt="frontend" width="75%" height="75%" />



## Sign up for Cruddur
- Create a new user
![notif404](./image/login1.JPG)
- Confirm your email and enter confirmation code as **1234**
![notif404](./image/login3.JPG)

- We have not set up notifications features for our app hence it returns **404 Not Found**
![notif404](./image/notif404.JPG)



## Implementing notification feature


- Update the [openAPI](/workspace/aws-bootcamp-cruddur-2023/backend-flask/openapi-3.0.yml) file to add an endpoint for notifications.

<img src="image/notifendpoint.JPG" alt="notifendpoint" width="75%" height="75%" />

### Updating the backend for the app to create an endpoint.

- Defining a route for notifications inside [app.py](/workspace/aws-bootcamp-cruddur-2023/backend-flask/app.py)

<img src="image/notifactivities1.JPG" alt="notifactivities1" width="75%" height="75%" />

- Creating a new service for notifications inside [notifications_activities.py](/workspace/aws-bootcamp-cruddur-2023/backend-flask/services/notifications_activities.py)

<img src="image/notificationspy.JPG" alt="notificationspy" width="75%" height="75%" />

- Import the service inside [app.py](/workspace/aws-bootcamp-cruddur-2023/backend-flask/app.py)

<img src="image/import.JPG" alt="import" width="75%" height="75%" />

- Verify the endpoint ```/api/activities/notifications``` to see if it returns the json data.

<img src="image/endp.JPG" alt="endp" width="75%" height="75%" />

### Updating the frontend for the app to create an endpoint.

- Create a [NotificationsFeedPage](/workspace/aws-bootcamp-cruddur-2023/frontend-react-js/src/pages/NotificationsFeedPage.js)

<img src="image/feedpage.JPG" alt="feedpage" width="75%" height="75%" />

- Import the page inside [App.js](/workspace/aws-bootcamp-cruddur-2023/frontend-react-js/src/App.js)

<img src="image/import1.JPG" alt="import1" width="75%" height="75%" />

- Add a new path for the notifications page in react

<img src="image/path.JPG" alt="path" width="75%" height="75%" />

- View the feed by clicking notifications. 

<img src="image/notifactivities2.JPG" alt="notifactivities2" width="75%" height="75%" />

## Adding DynamoDB Local and Postgres

## Homework Challenges 
