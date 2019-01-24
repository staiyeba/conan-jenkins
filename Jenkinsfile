def artifactory_name = "kristianj"
def artifactory_repo = "includeos-develop"
def repo_url = 'https://github.com/hioa-cs/IncludeOS.git'
def repo_branch = 'conan'

def conan_user = 'includeos'
def conan_channel = 'tools'

pipeline {
  agent {
    label 'conan-worker-2'
  }

  // should create a library for these lists of values
  parameters {
    string(name: 'Dependencies', defaultValue: 'musl')
    string(name: 'DepLocation', defaultValue: '')
    string(name: 'Versions', defaultValue: 'v1.1.18')
    string(name: 'Build_types', defaultValue: 'Release, Debug')
    string(name: 'Target_Architectures', defaultValue: 'x86_64, x86')
    string(name: 'Target_OS', defaultValue: 'Linux')
    string(name: 'Profiles', defaultValue: 'clang-6.0-linux-i386, clang-6.0-linux-x86_64')
    string(name: 'ProfilesToolchain', defaultValue: 'clang-6.0-linux-i386-toolchain, clang-6.0-linux-x86_64-toolchain')
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
            def target_os = "${params.Target_OS}"
            def target_architectures = "${params.Target_Architectures}".replaceAll("\\s", "").split(',')
            def build_types = "${params.Build_types}".replaceAll("\\s", "").split(',')
            def profiles = "${params.Profiles}".replaceAll("\\s", "").split(',')
            def profiles_toolchain = "${params.ProfilesToolchain}".replaceAll("\\s", "").split(',')
            def compiler_version = "${params.CompilerVer}"
            def dep_location = "${params.DepLocation}"
            currentBuild.description = "#${BUILD_NUMBER}, branch ${dependencies}"
            def builds = [:]


            for (prof_toolchain in profiles_toolchain) {
              for (prof in profiles) {
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
                                    -pr ${prof} ${conan_user}/${conan_channel}

                                    if [ -z "${dep_location}" ];
                                    then
                                      echo "creating ${dependencies}"
                                      conan create conan/${dependencies}/${versions} \
                                      -s build_type=${build} \
                                      -pr ${prof} ${conan_user}/${conan_channel}
                                    else
                                      echo "creating ${dep_location}/${dependencies}"
                                      conan create conan/${dep_location}/${dependencies} \
                                      -s build_type=${build} \
                                      -pr ${prof} ${dependencies}/${versions}@${conan_user}/${conan_channel}
                                    fi
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
