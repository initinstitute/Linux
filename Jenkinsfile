pipeline{
  agent none
  stages{
    stage(trial){
      agent{
        label 'agent1'
      }
      steps{
        echo "this is from github jenkins file"
      }
    }
    stage(stage2){
        agent{
        label 'agent1'
      }
      steps{
        sh"sleep 90"
      }
    }
     stage(stage3){
         agent{
        label 'agent1'
      }
      steps{
        echo "pipeline is completed"
      }
    }
  }
}
