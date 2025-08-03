<h2> Microservices </h2>

<h3> Flask - Redis - Python - Node.js </h3>

This is a four-tier application repository, including Dockerfile for containerization.

<ol>
  <li> There is an "app.py" which runs a flask app. This app loads an HTML page which allows the user to provide two inputs. </li>
  <li> The second tier is a redis container (or pod) which stores these two values. </li>
  <li> The third tier is a back-end python app that retrieves these two values, checks whether they are numerical inputs or not, in case they are numerical and correspond to year and month (date values)-like figures, it produces a monthly calendar. </li>
  <li> The forth tier is a node.js app which displays the values and the calendar. </li>
</ol>

Repository also includes pod definition files and service definition files, for kubernetes deployment.

<hr>

<h3>
  The flask app
</h3>
  <p> Containerized application exposes port 5000. A nodeport service exposes port 30090 on localhost. The app attempts to connect to "redis-service" on port 6379. It will check the connection, and in case of connection issues, it will return an error message. Upon successful insertion of values in the redis pod, it will trigger the python app and notify the user on the html page. </p>

<hr>

<h3> The Redis </h3>
  <p> Redis pod is a simple key-value based data-base. It is important to create a ClusterIP service for other pods to be able push (set) and pull (get) values from this data-base. </p>

<hr>
    
<h3> Python </h3>
  <p> The Python app retrieves two values from redis; then checks whether they are numerical or not. If the second values is a number between 1 and 12, it will produce a calendar (first value is presumably year, and second value, month). Python app also creates a DataFram (with pandas) and inserts the two variables retrieved from redis. Then saves the pandas DataFrame in csv format. This allows the administrator to check all the values inserted into the app, during the deployment. (Writing PV and PVC is highly recommended). It will then allow the nodejs to retrieve this monthly calendar.</p>

<hr>

<h3> Node js </h3>
  <p> The node js app is fairly simply script, which attempts to connect to the redis, using 6379 port which we had earlier exposed through redis ClusterIP service. It will also attempt to connect to the python pod, using port 5004 which is reachable through python ClusterIP service. Upon retrieving two values from redis and a calendar from python, Node JS will display them on an html page. A node port service is necessary for this pod, so that users can view the results. The nodeport service definition exposes port 30096 for node js.</p>

<hr>

<h2> Deployment </h2>

  <p> In order to start using the application, start by using the "Docker build" command and the dockerfiles provided here, to build the app images (flask, nodejs, and python). Be mindful and deliberate with the names you decide to give the images (tag them with). Later, when you want to create the pods (whether with "kubectl run ..." or pod-definition.yaml or deployment.yaml, you should know what is the name of the image. In general, be mindful of these items:
  
  <ul>
    <li> image names: you will later need it for creating the container from this image name </li>
    <li> container names </li>
    <li> app names: you will later need it for attaching services to these apps.</li>
  </ul>

  After creating the image, use one of the pod-definition.yaml manifests to test-run the container. You can check the container health by "kubctl get pods -o wide" command. If all is well, you can go ahead and create a service. It would be advisable to start with flask app, and then nodeport service. Make sure you the name you give to the app is also specified correctly in the service definition file.
  </p>

  <p>
    If all goes well, you can move on to the redis container. We don't need a dockerfile for this container, because the defualt redis container is capable of handling simple commands like SET or GET. We do, however, need to be mindful of the clusterIP service that we create for our redis pod, because the clusterIP service is what makes the communications possible. So, remember port 6379.
  </p>

  <p>
    If all goes well, and redis container is created, and redis clusterIP service is functioning well, you must be able to push (or SET) two values from the flask app, to the redis container. If you want to check this, the best way is to make sure both services (nodeport service for flask app, and clusterIP service for redis) have the correct endpoint. Perform this check by using the command "kubectl get svc". This command will give you a thorough view of the service. Look at the field "Endpoints". This should match the IP address of the pods in question. We need to make sure two connection are established flawlessly: one, nodeport service for accessing the flask app; two, clusterIP service - for allowing the flask app to insert values to the redis database. That is all for now!
  </p>

  <p> </p>
