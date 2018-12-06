UP <https://github.com/CCI-MOC/moc-public/wiki/Adding-Limits>

Create a file containing:

        apiVersion: "v1"
        kind: "LimitRange"
        metadata:
          name: "core-resource-limits" 
        spec:
          limits:
            - type: "Pod"
              max:
                cpu: "2" 
                memory: "5Gi" 
              min:
                cpu: "200m" 
                memory: "6Mi" 
            - type: "Container"
              max:
                cpu: "2" 
                memory: "5Gi" 
              min:
                cpu: "100m" 
                memory: "4Mi" 
              default:
                cpu: "300m" 
                memory: "200Mi" 
              defaultRequest:
                cpu: "200m" 
                memory: "100Mi" 
              maxLimitRequestRatio:
                cpu: "10"
            - type: openshift.io/Image
              max:
                storage: 5Gi 
            - type: openshift.io/ImageStream
              max:
                openshift.io/image-tags: 20 
                openshift.io/images: 30
            - type: PersistentVolumeClaim
              min: 
                storage: "500Mi"
              max:
                storage: "5Gi"

    