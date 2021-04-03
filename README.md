# Using Custom Resources with Eclipse JKube

You can also provide Custom Resource fragments to Eclipse JKube's source directory
(`src/main/jkube`) and Eclipse JKube would pick them up and apply during the resource-
apply phase. If you're using Eclipse JKube earlier than v1.2.0, You would need to register names of the CustomResources you're using in the XML configuration like this:
```xml
        <configuration>
            <resources>
                <customResourceDefinitions>
                    <customResourceDefinition>podsets.demo.fabric8.io</customResourceDefinition>
                    <customResourceDefinition>dummies.demo.fabric8.io</customResourceDefinition>
                    <customResourceDefinition>crontabs.stable.example.com</customResourceDefinition>
                </customResourceDefinitions>
            </resources>
        </configuration>
```

**Note**: Above configuration would be ignores in v1.2.0 since CustomResourceDefinition are detected automatically.

Your CustomResource fragments must be suffixed by `*-cr.yml` which will tell Eclipse JKube that
provided fragment is a CustomResource rather than a regular Kubernetes resource. You can take a 
look at this project's `src/main/jkube` directory:
```
jkube-quarkus-custom-resource-fragments : $ tree src/main/jkube/
src/main/jkube/
├── crd.yaml
├── crontab-cr.yml
├── dummy-crd.yml
├── dummy-cr.yml
├── second-dummy-cr.yml
└── test2-cr.yml
```

You can issue `k8s:resource` goal for generating resources. Eclipse JKube would create opinionated/
enriched manifests for resources in `target/classes/META-INF/jkube/` directory. You can then apply these resources using `k8s:apply` goal. here is how it would look like:
```
jkube-custom-resource-fragments : $ mvn k8s:resource k8s:apply
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< org.acme:getting-started >----------------------
[INFO] Building getting-started 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- kubernetes-maven-plugin:1.2.0:resource (default-cli) @ getting-started ---
[INFO] k8s: Running generator quarkus
[INFO] k8s: quarkus: Using Docker image quay.io/jkube/jkube-java-binary-s2i:0.0.9 as base / builder
[INFO] k8s: Using resource templates from /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/src/main/jkube
[INFO] k8s: jkube-controller: Adding a default Deployment
[INFO] k8s: jkube-service: Adding a default service 'getting-started' with ports [8080]
[INFO] k8s: jkube-service-discovery: Using first mentioned service port '8080'
[INFO] k8s: jkube-revision-history: Adding revision history limit to 2
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/getting-started-service.yml resource
[WARNING] Unknown keyword $module - you should define your own Meta Schema. If the keyword is irrelevant for validation, just use a NonValidationKeyword
[WARNING] Unknown keyword existingJavaType - you should define your own Meta Schema. If the keyword is irrelevant for validation, just use a NonValidationKeyword
[WARNING] Unknown keyword javaOmitEmpty - you should define your own Meta Schema. If the keyword is irrelevant for validation, just use a NonValidationKeyword
[WARNING] Unknown keyword serializer - you should define your own Meta Schema. If the keyword is irrelevant for validation, just use a NonValidationKeyword
[WARNING] Unknown keyword deserializer - you should define your own Meta Schema. If the keyword is irrelevant for validation, just use a NonValidationKeyword
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/ats.cnat.programming-kubernetes.info-customerresourcedefinition resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/dummies.demo.fabric8.io-customerresourcedefinition resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/testresources.insujang.github.io-customerresourcedefinition resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/getting-started-deployment.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/demo.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/example-at.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/example-podset.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/first-dummy.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/foo.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/my-new-cron-object.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/second-dummy.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/valid-virtual-service.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/crontabs.stable.example.com-customerresourcedefinition resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/destinationrules.networking.istio.io-customerresourcedefinition resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/ingressroutes.traefik.containo.us-customerresourcedefinition resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/podsets.demo.fabric8.io-customerresourcedefinition resource
[INFO]
[INFO] --- kubernetes-maven-plugin:1.2.0:apply (default-cli) @ getting-started ---
[INFO] k8s: Using Kubernetes at https://192.168.39.93:8443/ in namespace default with manifest /home/rohaan/work/repos/jkube-testing-samples/jkube-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes.yml
[INFO] k8s: Creating a Service from kubernetes.yml namespace default name getting-started
[INFO] k8s: Created Service: target/jkube/applyJson/default/service-getting-started-3.json
[INFO] k8s: Creating a Custom Resource Definition from kubernetes.yml name ats.cnat.programming-kubernetes.info
[INFO] k8s: Created Custom Resource Definition result: ats.cnat.programming-kubernetes.info
[INFO] k8s: Creating a Custom Resource Definition from kubernetes.yml name crontabs.stable.example.com
[INFO] k8s: Created Custom Resource Definition result: crontabs.stable.example.com
[INFO] k8s: Applying CustomResourceDefinition destinationrules.networking.istio.io from kubernetes.yml
[INFO] k8s: Creating a Custom Resource Definition from kubernetes.yml name dummies.demo.fabric8.io
[INFO] k8s: Created Custom Resource Definition result: dummies.demo.fabric8.io
[INFO] k8s: Creating a Custom Resource Definition from kubernetes.yml name ingressroutes.traefik.containo.us
[INFO] k8s: Created Custom Resource Definition result: ingressroutes.traefik.containo.us
[INFO] k8s: Applying CustomResourceDefinition podsets.demo.fabric8.io from kubernetes.yml
[INFO] k8s: Creating a Custom Resource Definition from kubernetes.yml name testresources.insujang.github.io
[INFO] k8s: Created Custom Resource Definition result: testresources.insujang.github.io
[INFO] k8s: Creating a Deployment from kubernetes.yml namespace default name getting-started
[INFO] k8s: Created Deployment: target/jkube/applyJson/default/deployment-getting-started-3.json
[INFO] k8s: Created Custom Resource: traefik.containo.us/v1alpha1#IngressRoute default/demo
[INFO] k8s: Created Custom Resource: cnat.programming-kubernetes.info/v1alpha1#At default/example-at
[INFO] k8s: Created Custom Resource: demo.fabric8.io/v1alpha1#PodSet default/example-podset
[INFO] k8s: Created Custom Resource: demo.fabric8.io/v1#Dummy default/first-dummy
[INFO] k8s: Created Custom Resource: traefik.containo.us/v1alpha1#IngressRoute default/foo
[INFO] k8s: Created Custom Resource: stable.example.com/v1#CronTab default/my-new-cron-object
[INFO] k8s: Created Custom Resource: demo.fabric8.io/v1#Dummy default/second-dummy
[INFO] k8s: Created Custom Resource: networking.istio.io/v1beta1#VirtualService default/valid-virtual-service
[INFO] k8s: HINT: Use the command `kubectl get pods -w` to watch your pods start up
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  17.409 s
[INFO] Finished at: 2021-04-03T22:47:19+05:30
[INFO] ------------------------------------------------------------------------
jkube-custom-resource-fragments : $ kubectl get ingressroute
NAME   AGE
demo   17s
foo    16s
```

During undeploy phase, Eclipse JKube is going to delete all created resources:
```
jkube-custom-resource-fragments : $ mvn k8s:undeploy
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< org.acme:getting-started >----------------------
[INFO] Building getting-started 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- kubernetes-maven-plugin:1.2.0:undeploy (default-cli) @ getting-started ---
[INFO] k8s: Deleting Custom Resource networking.istio.io/v1beta1#VirtualService valid-virtual-service
[INFO] k8s: Deleting Custom Resource demo.fabric8.io/v1#Dummy second-dummy
[INFO] k8s: Deleting Custom Resource stable.example.com/v1#CronTab my-new-cron-object
[INFO] k8s: Deleting Custom Resource traefik.containo.us/v1alpha1#IngressRoute foo
[INFO] k8s: Deleting Custom Resource demo.fabric8.io/v1#Dummy first-dummy
[INFO] k8s: Deleting Custom Resource demo.fabric8.io/v1alpha1#PodSet example-podset
[INFO] k8s: Deleting Custom Resource cnat.programming-kubernetes.info/v1alpha1#At example-at
[INFO] k8s: Deleting Custom Resource traefik.containo.us/v1alpha1#IngressRoute demo
[INFO] k8s: Deleting resource Deployment default/getting-started
[INFO] k8s: Deleting resource CustomResourceDefinition default/testresources.insujang.github.io
[INFO] k8s: Deleting resource CustomResourceDefinition default/podsets.demo.fabric8.io
[INFO] k8s: Deleting resource CustomResourceDefinition default/ingressroutes.traefik.containo.us
[INFO] k8s: Deleting resource CustomResourceDefinition default/dummies.demo.fabric8.io
[INFO] k8s: Deleting resource CustomResourceDefinition default/destinationrules.networking.istio.io
[INFO] k8s: Deleting resource CustomResourceDefinition default/crontabs.stable.example.com
[INFO] k8s: Deleting resource CustomResourceDefinition default/ats.cnat.programming-kubernetes.info
[INFO] k8s: Deleting resource Service default/getting-started
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  5.277 s
[INFO] Finished at: 2021-04-03T22:51:21+05:30
[INFO] ------------------------------------------------------------------------
```
