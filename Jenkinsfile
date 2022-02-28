pipeline {
  agent any

  environment {
    _POLICY_REPO="policy-library"
    _TF_SA_EMAIL="517363332888-compute@developer.gserviceaccount.com"
    _STATE_BUCKET_NAME="hwefberuwhb"
    _PROJECT_ID="mimetic-surf-337013"
  }

  stages {
    stage('setup') {
      steps {
          sh '''
          echo "Setting up gcloud for impersonation"
          gcloud config set auth/impersonate_service_account ${_TF_SA_EMAIL}
          echo "Adding bucket information to backends"
          for i in `find -name "backend.tf"`; do sed -i "s/UPDATE_ME/${_STATE_BUCKET_NAME}/" $i; done
          '''
        }
    }
    stage('TF plan validate all') {
      steps {
          sh '''
          ./tf-wrapper.sh plan_validate_all ${BRANCH_NAME} ${WORKSPACE}/${_POLICY_REPO} ${_PROJECT_ID}
          '''
        }
    }
    stage('TF init') {
      steps {
          sh '''
          ./tf-wrapper.sh init $BRANCH_NAME
          '''
        }
    }
    stage('TF plan') {
      steps {
          sh '''
          ./tf-wrapper.sh plan $BRANCH_NAME
          '''
        }
    }
    stage('TF validate') {
      steps {
          sh '''
          ./tf-wrapper.sh validate ${BRANCH_NAME} ${WORKSPACE}/${_POLICY_REPO} ${_PROJECT_ID}
          '''
        }
    }
    // stage('TF wait for approval') {
    //   steps {
    //       script {
    //       def userInput = input(id: 'confirm', message: 'Apply Terraform?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Apply terraform', name: 'confirm'] ])
    //       }
    //     }
    // }
    stage('TF apply') {
      steps {
          sh '''
          ./tf-wrapper.sh apply $BRANCH_NAME
          '''
        }
    }
  }
}
