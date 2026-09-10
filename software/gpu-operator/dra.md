## ResourceClaim CR
- Instead of container specifying certain resource request, for non cpu/memory pod references a resource claim instead for devices request (i.e. gpu)
	```
	# Without DRA
	apiVersion: v1
	kind: Pod
	metadata:
	  name: gpu-workload
	spec:
	  containers:
	  - name: app
	    image: my-app

	    resources:
	      requests:
	        cpu: "4"
	        memory: "16Gi"
	      limits:
	        cpu: "4"
	        memory: "16Gi"
	        nvidia.com/mig-3g.40gb: 1
	```
	```
	# With DRA - request a specific MIG partition
	apiVersion: resource.k8s.io/v1
	kind: ResourceClaim
	metadata:
	  name: my-mig
	spec:
	  devices:
	    requests:
	    - name: mig
	      exactly:
	        deviceClassName: mig.nvidia.com
	        selectors:
	        - cel:
	            expression: >
	              device.attributes['gpu.nvidia.com'].profile == '3g.40gb'

	---
	apiVersion: v1
	kind: Pod
	metadata:
	  name: gpu-workload
	spec:
	  resourceClaims:
	  - name: gpu
	    resourceClaimName: my-mig

	  containers:
	  - name: app
	    image: my-app
	    resources:
	      requests:
	        cpu: "4"
	        memory: "16Gi"
	      limits:
	        cpu: "4"
	        memory: "16Gi"

	      claims:
	      - name: gpu
	```