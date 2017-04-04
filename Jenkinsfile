OC_LOG_LEVEL=3

node ('jdk8') {

    def WORKSPACE = pwd()
   
    def APP_DIR = "${WORKSPACE}/tsdrService"     
    
    def ENV_INT  = 'tsdr-int'
    def ENV_UAT  = 'tsdr-uat'
    def ENV_PROD = 'tsdr-prod'

    def TSDR_APP = 'tsdr-nodejs'
    def ALL_APPS = [TSDR_APP]

    //end defs

    stage 'Checkout'
        checkout scm
    
    stage "Binary Build"
        // NOTE: in theory this shouldn't be needed but run into issue where -n flag on commands isn't
        // being adhered to so this is the work around
        sh """
            set -x
            oc project ${ENV_INT}
        """

        sh """
           set -x
           oc start-build ${TSDR_APP} --from-dir='${APP_DIR}' -n ${ENV_INT} --wait --follow
        """


    stage "Integration Deploy"
        // NOTE: in theory this shouldn't be needed but run into issue where -n flag on commands isn't
        // being adhered to so this is the work around
        sh """
            set -x
            oc project ${ENV_INT}
        """

        deploy(ALL_APPS, ENV_INT, 'latest')

    stage "Integration Tests"
        sh """
            echo 'Need Tests'
        """

    stage "UAT Deploy"
        // NOTE: in theory this shouldn't be needed but run into issue where -n flag on commands isn't
        // being adhered to so this is the work around
        sh """
            set -x
            oc project ${ENV_UAT}
        """
        promote(ALL_APPS, ENV_INT, ENV_UAT, 'latest')
        deploy(ALL_APPS, ENV_UAT, 'latest')

    stage "User Acceptence Tests"
        sh """
            echo 'TODO'
        """

    stage "Approval"
        input 'Ready to go to Production?'

    stage "Production Deploy"
        // NOTE: in theory this shouldn't be needed but run into issue where -n flag on commands isn't
        // being adhered to so this is the work around
        sh """
            set -x
            oc project ${ENV_PROD}
        """

        promote(ALL_APPS, ENV_UAT, ENV_PROD, 'latest')
        deploy(ALL_APPS, ENV_PROD, 'latest')
}

/* Deploys the given application names to the given namspace.
 *
 * @param appNames  [Array]  Application names to deploy. Names must map to DeploymentConfigs
 *                           and ImageStreams of the same name.
 * @param namespace [String] Namespace to deploy the applicaitons in.
 * @param version   [String] Version of the applications (ImageStreams) to deploy.
 */
def deploy(appNames, namespace, version) {
    deploys = [:]
    for( an in appNames ) {
        def appName = an //if you use 'an' below, it will equal the last value in each parallel run
       
        deploys["${appName}"] = {
            sh """
                set -x
    
                # get the latest image reference from the image stream
                newImageReference=\$(oc get is ${appName} -n ${namespace} -o jsonpath="{.status.tags[?(@.tag==\\"${version}\\")].items[0].dockerImageReference}")
               
                # update the DeploymnetConfig to deploy the latest version
                oc patch dc/${appName} -n ${namespace} --loglevel=${OC_LOG_LEVEL} -p "
                  {\\"spec\\":
                    {\\"template\\":
                      {\\"spec\\":
                        {\\"containers\\":
                          [{
                            \\"name\\":\\"${appName}\\",
                            \\"image\\":\\"\${newImageReference}\\"
                          }]
                        }
                      }
                    }
                  }"
    
                # deploy the latest image
                oc deploy ${appName} -n ${namespace} --latest --follow --loglevel=${OC_LOG_LEVEL}
            """
        }
    }

    parallel deploys
}

/* Promotes an image from one namespace to another by tagging the image in the new namespace.
 *
 * @param imageStreamNames [Array]  ImageStream names to promote
 * @param srcNamespace     [String] Source namspace to promote the ImageStreams from.
 * @param destNamespace    [String] Destination namespace to promote the ImageStreams to.
 * @param version          [String] Version of the ImageStreams to promote.
 */
def promote(imageStreamNames, srcNamespace, destNamespace, version) {
    for ( is in imageStreamNames ) {
        def imageStream = is //if you use 'an' below, it will equal the last value in each parallel run

        sh """
          set -x
          oc tag ${srcNamespace}/${imageStream}:${version} ${destNamespace}/${imageStream}:${version} --alias=false --loglevel=${OC_LOG_LEVEL}
        """
    }
}


