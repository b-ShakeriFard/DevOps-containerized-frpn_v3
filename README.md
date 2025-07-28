This is a four-tier application repository, including Dockerfile for containerization.

1. There is an "app.py" which runs a flask app. This app loads an HTML page which allows the user to provide two inputs.
2. The second tier is a redis container (or pod) which stores these two values.
3. The third tier is a back-end python app that retrieves these two values, checks whether they are numerical inputs or not, in case they are numerical and correspond to year and month (date values) like figures, it produces a monthly calendar.
4. The forth tier is a node.js app which displays the values and the calendar.

Repository also includes pod definition files and service definition files, for kubernetes deployment.
