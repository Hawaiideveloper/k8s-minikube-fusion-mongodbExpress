## Contains [env variables](https://hub.docker.com/_/mongo ) for the mongodb container
### Values in the mongo-secret.yaml below in data must be in base64 encoded, the file can not have comments.
## If you have comments in your secret file you might get the message below see issue 1:
- screen shot below -
#### Command used to retrieve base64 is: 
```echo -n 'someValue' | base64```

### To make the secrets file referrable you do:
```kubectl apply -f mongo-secret.yaml```
#### Check the secret file for the values [mentioned](https://www.youtube.com/watch?v=X48VuDVv0do)(1:22:41)


### The values below came from the mongo-secret.yaml file
##### meta section
```
valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username 
              
```
### Once all shows well you type:
`kubectl apply -f mongo.yaml`

### To verify the deployment you type:
`kubectl get all`
- see screen shot below - 
### To validate if it is running we use
`kubectl get pod`
- see screen shot below -

### To see all the events that led up to current you issue:
`kubectl describe pod mongodb-deployment-5f844ff8fd-5rw8n`
* note that the suffix of: 5f844ff8fd-5rw8n could be different

### To minimize files within directory we will add a new document inside the mongo-secret.yaml using the "---" 

### TO connect the service to the correct POD you use:
```
spec:
  selector:
    app: mongodb
```

### Then we expose the port by using keyword "targetPort" within the mongo.yaml file and re-apply its new configs:
- see screen shot - 
`kubectl apply -f mongo.yaml`

### Then we verify its service is correct
`kubectl get service`
- see screen shot -

### To see the Service configs and Endpoint/Address of Pod (see screenshot)172.17.0.4 in action
`kubectl describe service mongodb-service`


### To see the IP address with wide output you type see screen shot:
`kubectl get pod -o wide`

### Configuration of mongo-express is at:
https://hub.docker.com/_/mongo-express


### Setting up a config will eliminate the manual updating of new pod configs and centrally get things manages and name the URL the actual service - see screenshot -


### Then in the mongo-express we reference the file mongo-configmap.yaml within mongo-express
like:
```
 - name: ME_CONFIG_MONGODB_SERVER
          valueFrom:
            configMapKeyRef:
              name: mongodb-configmap
              key: mongo-root-password
```

### Now we apply both files with:
`kubectl apply -f mongo-configmap.yaml `
`kubectl apply -f mongo-express.yaml `

## Then we check to see the status by:
`kubectl get pod `
-see screen shot below-

## To review the logs you need to visit:
`kubectl logs mongo-express-98c6ff4b4-xzpf4 `
-see screen shot aug-28-2:55-

### To create an External Service aka Egress aka public facing
See mongo-express.yaml third page/section.
 ```
 apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
spec:
  selector:
    app: mongo-express
  type: LoadBalancer  <---- accepts external requests for an external IP
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: <--- Is the external port where the IP will be open must be between (30000 - 32767)
```

#### Need to apply the file for changes to take effect:
`kubectl apply -f mongo-express.yaml `

### Verify its working
`kubectl get service`
-see screen shot-