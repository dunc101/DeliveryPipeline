node {
    /********** DO NOT REMOVE BELOW!!! *******/
	VARIABLES_SECTION
	/********** DO NOT REMOVE ABOVE!!! *******/
	
	/************************************************************************
	** def repourl = properties."repourl"                                  **
	** def pomdirectory = properties."pomdirectory"                        **
	** def app = properties."app"                                          **
	** def readinessprobe = properties."readinessprobe"                    **
	** def livenessprobe = properties."livenessprobe"                      **
	** def replicas = properties."replicas"                                **
	*************************************************************************/
    println "RepoUrl -> $repourl\nPom Directory -> $pomdirectory\nApplication -> $app\nReadiness Probe -> $readinessprobe\nLiveness Probe -> $livenessprobe\nReplicas -> $replicas"
	
    stage 'Checkout and Build'
    build job: 'demo-checkoutandbuild', parameters: [[$class: 'StringParameterValue', name: 'GITURL', value: "$repourl"], [$class: 'StringParameterValue', name: 'POMDIRECTORY', value: "$pomdirectory"], [$class: 'StringParameterValue', name: 'APP', value: "$app"], [$class: 'StringParameterValue', name: 'TAG', value: "build${env.BUILD_NUMBER}"]]
    
    stage 'Push to Dev'
    build job: 'demo-dev', parameters: [[$class: 'StringParameterValue', name: 'TAG', value: "build${env.BUILD_NUMBER}"], [$class: 'StringParameterValue', name: 'APP', value: "$app"], [$class: 'StringParameterValue', name: 'READINESSPROBE', value: "$readinessprobe"], [$class: 'StringParameterValue', name: 'LIVENESSPROBE', value: "$livenessprobe"]]
    
    stage 'Deploy to Test'
    build job: 'demo-test', parameters: [[$class: 'StringParameterValue', name: 'TAG', value: "build${env.BUILD_NUMBER}"], [$class: 'StringParameterValue', name: 'APP', value: "$app"], [$class: 'StringParameterValue', name: 'READINESSPROBE', value: "$readinessprobe"], [$class: 'StringParameterValue', name: 'LIVENESSPROBE', value: "$livenessprobe"]]
    
    stage 'Request Authorization to Promote to Stage'
    def changelogs=readFile("/tmp/${app}/revisionlogs")
      input message: "Please approve the promotion to the Stage environment.  All tests and builds have passed.  The change logs are as follows: \n" + 
                    "--------------------------------------------------------------------\n" +
                    "${changelogs}" +
                    "--------------------------------------------------------------------", ok: 'Approve'

    stage 'Deploying to stage'
    build job: 'demo-stage', parameters: [[$class: 'StringParameterValue', name: 'TAG', value: "build${env.BUILD_NUMBER}"], [$class: 'StringParameterValue', name: 'APP', value: "$app"], [$class: 'StringParameterValue', name: 'READINESSPROBE', value: "$readinessprobe"], [$class: 'StringParameterValue', name: 'LIVENESSPROBE', value: "$livenessprobe"], [$class: 'StringParameterValue', name: 'REPLICAS', value: "$replicas"]]
}
