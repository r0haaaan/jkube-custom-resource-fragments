# Using Custom Resources with Eclipse JKube

You can also provide Custom Resource fragments to Eclipse JKube's source directory
(`src/main/jkube`) and Eclipse JKube would pick them up and apply during the resource-
apply phase. You would need to register names of the CustomResources you're using in
the XML configuration like this:
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
enriched manifests for Kubernetes resources in `target/classes/META-INF/jkube/` directory. Your
CustomResources would go in `custom-resources` subdirectory inside this directory.
```
jkube-quarkus-custom-resource-fragments : $ mvn k8s:resource
[INFO] Scanning for projects...
[INFO] 
[INFO] ----------------------< org.acme:getting-started >----------------------
[INFO] Building getting-started 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- kubernetes-maven-plugin:1.1.0-SNAPSHOT:resource (default-cli) @ getting-started ---
[INFO] k8s: Running generator quarkus
[INFO] k8s: quarkus: Using Docker image quay.io/jkube/jkube-java-binary-s2i:0.0.8 as base / builder
[INFO] k8s: Using resource templates from /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/src/main/jkube                                                                                                                    
[INFO] k8s: jkube-controller: Adding a default Deployment
[INFO] k8s: jkube-service: Adding a default service 'getting-started' with ports [8080]
[INFO] k8s: jkube-customresource: Processing CustomResource fragment /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/src/main/jkube/second-dummy-cr.yml                                                                     
[INFO] k8s: jkube-customresource: Processing CustomResource fragment /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/src/main/jkube/test2-cr.yml                                                                            
[INFO] k8s: jkube-customresource: Processing CustomResource fragment /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/src/main/jkube/dummy-cr.yml                                                                            
[INFO] k8s: jkube-customresource: Processing CustomResource fragment /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/src/main/jkube/crontab-cr.yml                                                                          
[INFO] k8s: jkube-revision-history: Adding revision history limit to 2
[INFO] k8s: validating /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/getting-started-service.yml resource                                                                        
[WARNING] Unknown keyword $module - you should define your own Meta Schema. If the keyword is irrelevant for validation, just use a NonValidationKeyword
[WARNING] Unknown keyword existingJavaType - you should define your own Meta Schema. If the keyword is irrelevant for validation, just use a NonValidationKeyword
[WARNING] Unknown keyword javaOmitEmpty - you should define your own Meta Schema. If the keyword is irrelevant for validation, just use a NonValidationKeyword
[WARNING] Unknown keyword serializer - you should define your own Meta Schema. If the keyword is irrelevant for validation, just use a NonValidationKeyword
[WARNING] Unknown keyword deserializer - you should define your own Meta Schema. If the keyword is irrelevant for validation, just use a NonValidationKeyword
[INFO] k8s: validating /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/dummies.demo.fabric8.io-customerresourcedefinition resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/testresources.insujang.github.io-customerresourcedefinition resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes/getting-started-deployment.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/target/classes/META-INF/jkube/custom-resources/second-dummy-cr.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/target/classes/META-INF/jkube/custom-resources/test2-cr.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/target/classes/META-INF/jkube/custom-resources/dummy-cr.yml resource
[INFO] k8s: validating /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/target/classes/META-INF/jkube/custom-resources/crontab-cr.yml resource
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  5.847 s
[INFO] Finished at: 2021-01-12T23:44:26+05:30
[INFO] ------------------------------------------------------------------------
jkube-quarkus-custom-resource-fragments : $ ls target/classes/META-INF/jkube/
custom-resources  kubernetes  kubernetes.yml  openshift  openshift.yml
jkube-quarkus-custom-resource-fragments : $ ls target/classes/META-INF/jkube/custom-resources/
crontab-cr.yml  dummy-cr.yml  second-dummy-cr.yml  test2-cr.yml
```

These resources would be applied during the apply phase (`mvn k8s:apply`):
```
mvn k8s:apply
[INFO] Scanning for projects...
[INFO] 
[INFO] ----------------------< org.acme:getting-started >----------------------
[INFO] Building getting-started 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- kubernetes-maven-plugin:1.1.0-SNAPSHOT:apply (default-cli) @ getting-started ---
[INFO] k8s: Using Kubernetes at https://192.168.39.3:8443/ in namespace default with manifest /home/rohaan/work/repos/jkube-quarkus-custom-resource-fragments/target/classes/META-INF/jkube/kubernetes.yml 
[INFO] k8s: Creating a Service from kubernetes.yml namespace default name getting-started
[INFO] k8s: Created Service: target/jkube/applyJson/default/service-getting-started.json
[INFO] k8s: Creating a Custom Resource Definition from kubernetes.yml name dummies.demo.fabric8.io
[INFO] k8s: Created Custom Resource Definition result: dummies.demo.fabric8.io
[INFO] k8s: Creating a Custom Resource Definition from kubernetes.yml name testresources.insujang.github.io
[INFO] k8s: Created Custom Resource Definition result: testresources.insujang.github.io
[INFO] k8s: Creating a Deployment from kubernetes.yml namespace default name getting-started
[INFO] k8s: Created Deployment: target/jkube/applyJson/default/deployment-getting-started.json
[INFO] k8s: Created Custom Resource: demo.fabric8.io/v1alpha1#PodSet example-podset
[INFO] k8s: Created Custom Resource: demo.fabric8.io/v1#Dummy first-dummy
[INFO] k8s: Created Custom Resource: demo.fabric8.io/v1#Dummy second-dummy
[INFO] k8s: Created Custom Resource: stable.example.com/v1#CronTab my-new-cron-object
[INFO] k8s: HINT: Use the command `kubectl get pods -w` to watch your pods start up
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  7.908 s
[INFO] Finished at: 2021-01-12T23:46:48+05:30
[INFO] ------------------------------------------------------------------------
jkube-quarkus-custom-resource-fragments : $ kubectl get dummy
NAME           AGE
first-dummy    7s
second-dummy   7s
jkube-quarkus-custom-resource-fragments : $ kubectl get crontab
NAME                 AGE
my-new-cron-object   11s
jkube-quarkus-custom-resource-fragments : $ 
```

During undeploy phase, Eclipse JKube is going to delete all created resources:
```
jkube-quarkus-custom-resource-fragments : $ mvn k8s:undeploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ----------------------< org.acme:getting-started >----------------------
[INFO] Building getting-started 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- kubernetes-maven-plugin:1.1.0-SNAPSHOT:undeploy (default-cli) @ getting-started ---
[INFO] k8s: Deleting Custom Resource demo.fabric8.io/v1alpha1#PodSet example-podset
[INFO] k8s: Deleting Custom Resource demo.fabric8.io/v1#Dummy first-dummy
[INFO] k8s: Deleting Custom Resource demo.fabric8.io/v1#Dummy second-dummy
[INFO] k8s: Deleting Custom Resource stable.example.com/v1#CronTab my-new-cron-object
[INFO] k8s: Deleting resource Deployment default/getting-started
[INFO] k8s: Deleting resource CustomResourceDefinition default/testresources.insujang.github.io
[INFO] k8s: Deleting resource CustomResourceDefinition default/dummies.demo.fabric8.io
[INFO] k8s: Deleting resource Service default/getting-started
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  4.079 s
[INFO] Finished at: 2021-01-12T23:47:49+05:30
[INFO] ------------------------------------------------------------------------
```
