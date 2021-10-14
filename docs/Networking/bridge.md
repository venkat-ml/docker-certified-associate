Create the Bridge Network
Create a bridge network called prices-net.

```
docker network create --driver bridge prices-net
```

Create the base-price Container
Create a container for the component that serves base prices.

```
docker run --name base-price -d --network prices-net linuxacademycontent/prices-base-price:1
```

Create the sales Container
Create a container for the component that serves products on sale.

```
docker run --name sales -d --network prices-net linuxacademycontent/prices-sales:1
```


Create the total-price Container
Create a container for the component that serves the total prices of products.


```
docker run --name total-price -d --network prices-net -p 8080:80 linuxacademycontent/prices-total-price:1
```

Verify that everything is set up correctly.

```
curl localhost:8080
```

You should get a list of products and their final prices. The total-prices container calculates these prices by first querying the other two containers. This communication takes place over the prices-net bridge network.