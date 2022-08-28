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

![Screen Shot 2022-08-28 at 1 00 38 PM](https://user-images.githubusercontent.com/13468708/187091979-ff9627fc-f2da-4b30-bdec-14339cf5bc42.png)


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

![Screen Shot 2022-08-28 at 2 06 15 PM](https://user-images.githubusercontent.com/13468708/187091954-188ccfb7-b304-4087-a1da-b59ec4b76783.png)


### Then we verify its service is correct
`kubectl get service`
- see screen shot -
![Screen Shot 2022-08-28 at 2 04 42 PM](https://user-images.githubusercontent.com/13468708/187091930-d6219d66-a0db-460b-ad32-6aa02bda45de.png)


### To see the Service configs and Endpoint/Address of Pod (see screenshot)172.17.0.4 in action
`kubectl describe service mongodb-service`
![Screen Shot 2022-08-28 at 2 12 26 PM](https://user-images.githubusercontent.com/13468708/187091917-d003b974-2aac-434a-baf1-a61e922ef97c.png)


### To see the IP address with wide output you type see screen shot:
`kubectl get pod -o wide`

![Screen Shot 2022-08-28 at 2 14 14 PM](https://user-images.githubusercontent.com/13468708/187091898-8fa8a6cf-cddc-475b-ba71-008d12c12298.png)

### Configuration of mongo-express is at:
https://hub.docker.com/_/mongo-express


### Setting up a config will eliminate the manual updating of new pod configs and centrally get things manages and name the URL the actual service - see screenshot -
![Screen Shot 2022-08-28 at 2 37 35 PM](https://user-images.githubusercontent.com/13468708/187091883-d7c02a51-b97d-427a-8ac1-781cd2595e75.png)


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

![Screen Shot 2022-08-28 at 2 51 12 PM](https://user-images.githubusercontent.com/13468708/187091866-1248f452-b0ea-4c4e-8f21-8dd7ad809d7b.png)


## To review the logs you need to visit:
`kubectl logs mongo-express-98c6ff4b4-xzpf4 `
-see screen shot aug-28-2:55-
![Screen Shot 2022-08-28 at 2 54 56 PM](https://user-images.githubusercontent.com/13468708/187091847-da07ed44-d0cb-490c-bca7-5f0835dbbe53.png)

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

![Screen Shot 2022-08-28 at 3 08 16 PM](https://user-images.githubusercontent.com/13468708/187091833-440f86fa-c2b0-4d9c-9e03-78df401f2c55.png)
