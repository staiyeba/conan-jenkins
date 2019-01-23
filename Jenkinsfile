def artifactory_name = "kristianj"
def artifactory_repo = "includeos-develop"
def repo_url = 'https://github.com/hioa-cs/IncludeOS.git'
def repo_branch = 'conan'

def conan_user = 'kristian'
def conan_channel = 'demo'

pipeline {
  agent {
    label 'conan-worker-2'
  }

  // should create a library for these lists of values
  parameters {
    string(name: 'Dependencies', defaultValue: 'musl')
    string(name: 'Versions', defaultValue: 'v1.1.18')
    string(name: 'Build_types', defaultValue: 'Release, Debug')
    string(name: 'Target_Architectures', defaultValue: 'x86_64, x86')
    string(name: 'Target_OS', defaultValue: 'Linux, Macos')
    string(name: 'Profiles', defaultValue: 'clang-6.0-linux-i386, clang-6.0-linux-x86_64')
    // keeping compiler version out of this, assuming it will be part of the profiles
    string(name: 'CompilerVer', defaultValue: '6.0') //clang version
  }

  stages {
      stage('Build') {
        steps {
          // should check if size of parameter is one, then no need to remove quotations
          script {
            def dependencies = "${params.Dependencies}"
            def versions = "${params.Versions}"
            def target_os = "${params.Target_OS}".replaceAll("\\s", "").split(',')
            def target_architectures = "${params.Target_Architectures}".replaceAll("\\s", "").split(',')
            def build_types = "${params.Build_types}".replaceAll("\\s", "").split(',')
            def profiles = "${params.Profiles}".replaceAll("\\s", "").split(',')
            def compiler_version = "${params.CompilerVer}"

            def builds = [:]

            for (prof in profiles) {
              for (t_os in target_os) {
                for (t_arch in target_architectures) {
                  for (build in build_types) {
                    String buildName = "${dependencies}-${build}-${t_arch}-${prof}"

                    builds[buildName] = {
                      node('conan-worker-2') {
                        stage(buildName){
                                git branch: repo_branch, url: repo_url
                                sh """
                                  echo "creating binutils"
                                  conan create conan/gnu/binutils/2.31 \
                                  -s compiler.version=${compiler_version} \
                                  -s build_type=${build} \
                                  -s arch=${t_arch} \
                                  -s os=${t_os} \
                                  -pr ${prof} ${conan_user}/${conan_channel}

                                  echo "creating ${dependencies}"
                                  conan create conan/musl/${versions} \
                                  -s build_type=${build} \
                                  -s arch=${t_arch} \
                                  -s os=${t_os} \
                                  -pr ${prof} ${conan_user}/${conan_channel}
                                """
                        }
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
