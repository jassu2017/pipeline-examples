node {
    stage('Checkout external proj') {
      steps{
          git branch: 'master',
          credentialsId: '747db20a-526b-4716-8503-2d1343202f4f',
          url: 'ssh://github.com/jassu2017/pipeline-examples.git'
            
            sh "ls -lat"
            }
    }
            
    stage "Create build output"
    
    // Make the output directory.
    sh "mkdir -p output"

    // Write an useful file, which is needed to be archived.
    writeFile file: "output/usefulfile.txt", text: "This file is useful, need to archive it."

    // Write an useless file, which is not needed to be archived.
    writeFile file: "output/uselessfile.md", text: "This file is useless, no need to archive it.delete it"

    stage "Archive build output"
    
    // Archive the build output artifacts.
    archiveArtifacts artifacts: 'output/*.txt', excludes: 'output/*.md'
}
