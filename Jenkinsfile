node {
   def projectName="eai-drivetrain-brokerrep-sys"
   def mvnHome
   stage('Preparation') { // for display purposes
      git url: "git@gitawse1.hagerty.com:Hagerty/${projectName}.git", branch: env.BRANCH_NAME
      bat(/git clean -f/)
      bat(/git reset --hard/)
      bat(/git checkout ./)
      mvnHome = tool 'M3'
   } 
   stage('BuildAll') {
      if (env.BRANCH_NAME=='integration') {
            bat(/"${mvnHome}\bin\mvn" clean deploy -DskipDeployment=true/)
      } else if (env.BRANCH_NAME=='master') {
         def pom = readMavenPom file: 'pom.xml'
         def versionList = pom.version.replace("-SNAPSHOT", "").tokenize(".")
         def newRelease = Eval.me(versionList[2])+1
         def version = "${versionList[0]}.${versionList[1]}.${versionList[2]}"
         stage('masterRelease') {
            bat(/"${mvnHome}\bin\mvn" clean versions:set -DnewVersion=${version}/)
            bat(/"${mvnHome}\bin\mvn" clean deploy/)
         }
         stage('tagVersion') {
            bat(/git tag -f ${projectName}-${version} -m 'JenkinsRelease'/)
            bat(/git push -u origin master --tag/)
         }
         stage('updateIntegrationPOMVersion') {
            bat(/git checkout ./)
             bat(/git fetch/)
            bat(/git checkout integration/)
            bat(/git clean -f/)
            bat(/git reset --hard/)
            bat(/git checkout ./)
            bat(/git pull origin integration/)
            bat(/"${mvnHome}\bin\mvn" clean versions:set -DnewVersion=${versionList[0]}.${versionList[1]}.${newRelease}-SNAPSHOT/)
            bat(/git add -- pom.xml/)
            bat(/git commit -m 'jenskinsChangingVersion'/)
            bat(/git push -u origin integration/)
         }
      }
   }
}