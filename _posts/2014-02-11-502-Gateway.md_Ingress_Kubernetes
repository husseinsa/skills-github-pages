 Ingress 502 Bad Gateway
 - Get Ingress controller External IP address   
    
    ![Alt text](<502-gateway/1- services.png>)  

 - Access the Rest API `curl http://20.246.239.92/petstoreproductservice/v2/product/info`   
    
    ![Alt text](<502-gateway/2- curl-ingress-502.png>)  

 - Troubleshooting
    -  Check if the pod is up and running `k get pods -l app==aks-petstoreproductservice`  
        ![Alt text](502-gateway/3-pods-status.png)
    -  Examine the pod logs `k logs <pod-name>` for any errors or issues. Found no issues.
    - Lets check the ingress configuration  
        ![Alt text](502-gateway/4-ingress-investigation.png)
     
      -  In the ingress  we can see the path which has product api 
        -  points to the right service with the right port (highlighted in yellow)
        -  points to the tight IP of the Pod highlighted in green

    -  Lets see where the connection breaks. First create a test pod and attach a terminal session to it
        - create a test pod and attach a terminal session to it   
        `kubectl run -it --rm aks-ingress-test --image=mcr.microsoft.com/dotnet/runtime-deps:6.0`
        - install curl `apt-get update && apt-get install -y curl`    
        - Hit the internal IP of the Ingress -> Access address of your api using service internal IP of ingress controller curl http://10.0.171.205/petstorepetservice/v2/pet/info
            
            ![Alt text](502-gateway/5-curl-ingress-internal-ip.png)
        - Hit the API service IP `curl http://10.0.33.254/petstorepetservice/v2/pet/info`. I got `curl: (7) Failed to connect to 10.0.33.254 port 80: Connection refused`
          Since we have already verified that the pod is up and running and this is a port issue. I will check the service target port and see if it's matching with the port on the container.
            - Run the command: `k describe service aks-petstoreproductservice` and check the target port. You can also figure it out from the Endpoints attribute.
                ![Alt text](502-gateway/6.png)
            - Run the command: ` k describe pod -l app==aks-petstoreproductservice` and check the Port of the container
                ![Alt text](502-gateway/7.png)

            we can see the service is trying to hit the pod on port 80, however the pod is listening on port 8080 and thats why we see the error `connection refused`
    - Lets change the target port of the service
        - Run `k edit service aks-petstoreproductservice`. Find the `targetPort` and change the value to 8080
        ![Alt text](502-gateway/8.png)
        - Describe the service again and see the changes that took effect
        ![Alt text](502-gateway/9.png)
    - Test again by accessing the internal IP of the ingress as in the first step
        ![Alt text](502-gateway/10.png)
