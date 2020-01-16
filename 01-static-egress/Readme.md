### Static IP Address EGress Traffic

```bash
az aks show --resource-group portworxrg --name k8sportworx --query nodeResourceGroup -o tsv
az network public-ip create \
   --resource-group MC_portworxrg_k8sportworx_westeurope \
     --name aks-egress-public-ip \
     --allocation-method static

```

```json
{
  "publicIp": {
    "ddosSettings": null,
    "dnsSettings": null,
    "etag": "W/\"3c5acec5-176f-40c1-bb34-8ebdebda57a4\"",
    "id": "/subscriptions/xxxxxxxxxxx/resourceGroups/MC_portworxrg_k8sportworx_westeurope/providers/Microsoft.Network/publicIPAddresses/aks-egress-public-ip",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "13.81.210.241",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "westeurope",
    "name": "aks-egress-public-ip",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "publicIpPrefix": null,
    "resourceGroup": "MC_portworxrg_k8sportworx_westeurope",
    "resourceGuid": "xxxxxxxxxxxxxx",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
}
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 13.81.210.241
  type: LoadBalancer
  ports:
  - port: 80
```

```bash
kubectl apply -f egress-service.yaml 
kubectl describe svc azure-egress
```

```
NAME           TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
azure-egress   LoadBalancer   10.0.60.42   13.81.210.241   80:32340/TCP   10m
kubernetes     ClusterIP      10.0.0.1     <none>          443/TCP        22d
```

```bash
kubectl run --restart=Never --image=raesene/alpine-nettools nettools
kubectl exec -it nettools -- /bin/sh
curl ifconfig.co
```