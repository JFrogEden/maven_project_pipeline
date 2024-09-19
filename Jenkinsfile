CRON_SETTINGS = '''* * * * *'''

pipeline {
    agent any

    triggers {
        cron(CRON_SETTINGS)
    }

    environment {

        // [Mandatory]
        // Username of the account associated with the Git access token
        JF_GIT_USERNAME = "JFrogEden"

        // [Mandatory]
        // Owner of the repository
        // BitbucketServer note: Private projects should start with the prefix: "~"
        JF_GIT_OWNER = "JfrogEden"

        // [Mandatory]
        // Name of the repository to scan
        JF_GIT_REPO = "maven_project_pipeline"

        // [Mandatory]
        // Name of the git provider, one of the following: [bitbucketServer,github,gitlab,azureRepos]
        JF_GIT_PROVIDER = "github"

        // [Mandatory]
        // JFrog platform URL (This functionality requires version 3.29.0 or above of Xray)
        JF_URL = "https://kimt.jfrog.io"

        // [Mandatory if JF_USER and JF_PASSWORD are not provided]
        // JFrog access token with 'read' permissions for Xray
        JF_ACCESS_TOKEN = credentials("JF_ACCESS_TOKEN")

        // [Mandatory]
        // GitHub enterprise server access token with the following permissions:
        // Read and Write access to code, pull requests, security events, and workflows
        JF_GIT_TOKEN = credentials("JF_GIT_TOKEN")

        // [Mandatory for on-premise]
        // API endpoint to VCS provider REST API
        JF_GIT_API_ENDPOINT = "https://api.github.com/"

        // [Optional]
        // If the machine that runs Frogbot has no access to the internet, set the name of a remote repository
        // in Artifactory, which proxies https://releases.jfrog.io
        // The 'frogbot' executable and other tools it needs will be downloaded through this repository.
        JF_RELEASES_REPO= ""

        // [Optional]
        // Avoid adding extra info to pull request comments. that isn't related to the scan findings.
        // JF_AVOID_EXTRA_MESSAGES= "TRUE"

        ///////////////////////////////////////////////////////////////////////////
        //   If your project uses a 'frogbot-config.yml' file, you should define //
        //   the following variables inside the file, instead of here.           //
        ///////////////////////////////////////////////////////////////////////////

        // [Mandatory if the two conditions below are met]
        // 1. The project uses yarn 2, NuGet or .NET to download its dependencies
        // 2. The `installCommand` variable isn't set in your frogbot-config.yml file.
        //
        // The command that installs the project dependencies (e.g "nuget restore")
        // JF_INSTALL_DEPS_CMD= ""

        // [Optional, default: "."]
        // Relative path to the root of the project in the Git repository
        // JF_WORKING_DIR= path/to/project/dir
        
        // [Default: "*.git*;*node_modules*;*target*;*venv*;*test*"]
        // List of exclusion patterns (utilizing wildcards) for excluding paths in the source code of the Git repository during SCA scans.
        // JF_PATH_EXCLUSIONS= "*.git*;*node_modules*;*target*;*venv*;*test*"

        // [Optional]
        // Xray Watches. Learn more about them here: https://www.jfrog.com/confluence/display/JFROG/Configuring+Xray+Watches
        // JF_WATCHES= <watch-1>,<watch-2>...<watch-n>

        // [Optional]
        // JFrog project. Learn more about it here: https://www.jfrog.com/confluence/display/JFROG/Projects
        // JF_PROJECT= <project-key>

        // [Optional, default: "FALSE"]
        // Displays all existing vulnerabilities, including the ones that were added by the pull request.
        // JF_INCLUDE_ALL_VULNERABILITIES= "TRUE"

        // [Optional, default: "TRUE"]
        // Fails the Frogbot task if any security issue is found.
        // JF_FAIL= "FALSE"

        // [Optional, default: "TRUE"]
        // Relative path to a Pip requirements.txt file. If not set, the Python project's dependencies are determined and scanned using the project setup.py file.
        // JF_REQUIREMENTS_FILE= ""

        // [Optional, Default: "TRUE"]
        // Use Gradle wrapper.
        // JF_USE_WRAPPER= "FALSE"

        // [Optional]
        // Frogbot will download the project dependencies if they're not cached locally. To download the
        // dependencies from a virtual repository in JFrog Artifactory set the name of the repository. There's no
        // need to set this value, if it is set in the frogbot-config.yml file.
        // JF_DEPS_REPO= ""

        // [Optional]
        // Template for the branch name generated by Frogbot when creating pull requests with fixes.
        // The template must include {BRANCH_NAME_HASH}, to ensure that the generated branch name is unique.
        // The template can optionally include the {IMPACTED_PACKAGE} and {FIX_VERSION} variables.
        // JF_BRANCH_NAME_TEMPLATE= "frogbot-{IMPACTED_PACKAGE}-{BRANCH_NAME_HASH}"

        // [Optional]
        // Template for the commit message generated by Frogbot when creating pull requests with fixes
        // The template can optionally include the {IMPACTED_PACKAGE} and {FIX_VERSION} variables.
        // JF_COMMIT_MESSAGE_TEMPLATE= "Upgrade {IMPACTED_PACKAGE} to {FIX_VERSION}"

        // [Optional]
        // Template for the pull request title generated by Frogbot when creating pull requests with fixes.
        // The template can optionally include the {IMPACTED_PACKAGE} and {FIX_VERSION} variables.
        // JF_PULL_REQUEST_TITLE_TEMPLATE= "[🐸 Frogbot] Upgrade {IMPACTED_PACKAGE} to to {FIX_VERSION}"

        // [Optional, Default: "FALSE"]
        // If TRUE, Frogbot creates a single pull request with all the fixes.
        // If FALSE, Frogbot creates a separate pull request for each fix.
        // JF_GIT_AGGREGATE_FIXES= "FALSE"

        // [Optional, Default: "FALSE"]
        // Handle vulnerabilities with fix versions only
        // JF_FIXABLE_ONLY= "TRUE"

        // [Optional]
        // Set the minimum severity for vulnerabilities that should be fixed and commented on in pull requests
        // The following values are accepted: Low, Medium, High, or Critical
        // JF_MIN_SEVERITY= ""

        // [Optional, Default: eco-system+frogbot@jfrog.com]
        // Set the email of the commit author
        // JF_GIT_EMAIL_AUTHOR: ""

        // [Optional]
        // Set the list of allowed licenses, The full list of licenses can be found in: 
        // https://github.com/jfrog/frogbot/blob/master/docs/licenses.md
        // JF_ALLOWED_LICENSES: "MIT, Apache-2.0"

        // [Optional]
        // Avoid adding extra info to pull request comments. that isn't related to the scan findings. 
        // JF_AVOID_EXTRA_MESSAGES: "TRUE"

        // [Optional]
        // Add a title to pull request comments generated by Frogbot. 
        // JF_PR_COMMENT_TITLE: ""
    }

    stages {

        stage('Download Frogbot') {
            steps {
                script{    
                    // For Linux / MacOS runner:
                    sh """ curl -fLg "https://releases.jfrog.io/artifactory/frogbot/v2/[RELEASE]/getFrogbot.sh" | sh"""
                    // For Windows runner:
                    // powershell """iwr https://releases.jfrog.io/artifactory/frogbot/v2/[RELEASE]/frogbot-windows-amd64/frogbot.exe -OutFile .\frogbot.exe"""
                        
                }
            }
        }

        stage('Scan Repository') {
            steps {
                sh "./frogbot scan-repository"
            }
        }
    }
}