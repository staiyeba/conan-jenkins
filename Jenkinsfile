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

// binutils
def binutils_version='2.31'
// applies for binutils as in resides in the gnu directory
def recipe_location = 'gnu'

//musl
def musl_version='v1.1.18'

//llv
def llv_version='7.0'

node {
   label 'conan_worker'

   def server
   def client
   def serverName

    stage("Get project"){
        //TODO replace with SCM snapshot.. if possible pull only the conan folder
        git branch: repo_branch, url: repo_url
        echo "$PATH"
    }

    stage("Build recipe - binutils"){
          sh """
            echo "creating binutils"
            conan create conan/${recipe_location}/binutils/${binutils_version} ${conan_user}/${conan_channel}
          """
    }

    stage("Build recipe - musl"){
          sh """
            echo "creating musl"
            conan create conan/musl/${musl_version} ${conan_user}/${conan_channel}

          """
    }

    stage("Build recipe - llv"){
          sh """
            echo "creating libunwind"
            conan create conan/llv/libunwind/${llv_version} ${conan_user}/${conan_channel}
            echo "creating libcxxabi"
            conan create conan/llv/libcxxabi/${llv_version} ${conan_user}/${conan_channel}
            echo "creating libcxx"
            conan create conan/llv/libcxx/${llv_version} ${conan_user}/${conan_channel}

          """
    }

    stage("Upload recipes to local library"){
          sh """
            echo "Uploading to local conan library"

          """
    }
}
