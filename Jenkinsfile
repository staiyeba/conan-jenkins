def artifactory_name = "kristianj"
def artifactory_repo = "includeos-develop"
def repo_url = 'https://github.com/hioa-cs/IncludeOS.git'
def repo_branch = 'conan'

def conan_user = 'kristian'
def conan_channel = 'demo'

pipeline {
  agent {
    label 'conan_pipe_worker'
  }

  // should create a library for these lists of values
  parameters {
    string(name: 'Dependencies', defaultValue: 'musl')
    string(name: 'Versions', defaultValue: 'v1.1.18')
    string(name: 'Build_types', defaultValue: 'Release, Debug')
    string(name: 'Target_Architectures', defaultValue: 'x86_64, x86')
    string(name: 'Target_OS', defaultValue: 'Linux')
    string(name: 'Profiles', defaultValue: 'clang-6.0-linux-i386, clang-6.0-linux-x86_64, gcc-8.2.0-linux-x86_64')
  }

  stages {
      stage('Build') {
        steps {

          script {
            def dependencies = "${params.Dependencies}"
            def versions = "${params.Versions}"
            def target_architectures = "${params.Target_Architectures}".replaceAll("\\s", "").split(',')
            def build_types = "${params.Build_types}".replaceAll("\\s", "").split(',')
            def profiles = "${params.Profiles}".replaceAll("\\s", "").split(',')

            def builds = [:]

            for (prof in profiles) {
              for (target_arch in target_architectures) {
                for (build in build_types) {
                  String buildName = "${dependencies}-${versions}-${build}-${prof}"

                  builds[buildName] = {
                    node('conan-worker-2') {
                      stage(buildName) {
                        build job: './recipe-musl',
                          parameters: [
                                  string(name: "Versions", value: "1.1.19"), // should be a variable from a list
                                  string(name: "Build_types", value: "Debug") // build_type = Debug / Release
                          ]
                      }
                    }
                  }
                }
              }
            }
            parallel builds
          }
        }
      }
  }
}
