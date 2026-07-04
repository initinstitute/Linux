pipeline{
  agent any
  stages{
    stage(trial){
      steps{
        echo "this is from github jenkins file"
      }
    }
    stage(stage2){
      steps{
        sh"sleep 90"
      }
    }
     stage(stage3){
      steps{
        echo "pipeline is completed"
      }
    }
  }
}
