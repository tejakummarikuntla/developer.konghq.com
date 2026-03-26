1. Install {{site.mesh_product_name}}:

   ```sh
   helm repo add kong-mesh https://kong.github.io/kong-mesh-charts
   helm repo update
   helm upgrade \
     --install \
     --create-namespace \
     --namespace kong-mesh-system \
     kong-mesh kong-mesh/kong-mesh
   kubectl wait -n kong-mesh-system --for=condition=ready pod --selector=app=kong-mesh-control-plane --timeout=90s
   ```
   {: data-test-prereq="block" }

1. Configure the default Mesh with mTLS enabled:

   ```sh
   echo "apiVersion: kuma.io/v1alpha1
   kind: Mesh
   metadata:
     name: default
   spec:
     mtls:
       backends:
       - name: ca-1
         type: builtin
       enabledBackend: ca-1" | kubectl apply -f -
   ```
   {: data-test-prereq="block" }

1. Allow all traffic in the mesh:

   ```sh
   echo "apiVersion: kuma.io/v1alpha1
   kind: MeshTrafficPermission
   metadata:
     name: allow-all
     namespace: kong-mesh-system
   spec:
     targetRef:
       kind: Mesh
     from:
       - targetRef:
           kind: Mesh
         default:
           action: Allow" | kubectl apply -f -
   ```
   {: data-test-prereq="block" }
