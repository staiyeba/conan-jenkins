//checkout repo
//build recipe
//deploy package
//basically
//for loop multi jobs..

def artifactory_name = "kristianj"
def artifactory_repo = "includeos-develop"
def repo_url = 'https://github.com/hioa-cs/IncludeOS.git'
def repo_branch = 'conan'

def conan_user = 'kristian'
def conan_channel = 'demo'

def build_type = ["Release", "Debug"]

def version = ["v1.1.18", "v1.1.19"]

def projects = [" "," "]


    def server
    def client
    def serverNamelabel

    stage("Get project"){
      node('conan-worker-1'){

        //TODO replace with SCM snapshot.. if possible pull only the conan folder
        git branch: repo_branch, url: repo_url
        echo "$PATH
      }
    }

    stage("Build/Get binutils"){
      node('conan_pipe_worker'){
        script {
            echo "should build or get binutils - turned off for now"
            // build job: "recipe-binutils" // builds binutils
        }
      }

    }

    stage("Build musl"){
        node ('conan_pipe_worker'){
          script {
            for(int i=0; i<=build_type.size(); i++){
                def tasks = [:]
                for(int j=0; j<build_type.size(); j++){
                    def build_ver = version[i]
                    def build_set = build_type[i]
                    def label = build_set
                    tasks[label] = { -> build job: "recipe-musl",
                        parameters: [
                                string(name: "version", value: "v1.1.19"), // should be a variable from a list
                                string(name: "build_type", value: build_set) // build_type = Debug / Release
                        ]
                    }

                }

            }
          }
        }

    }
parallel projects
