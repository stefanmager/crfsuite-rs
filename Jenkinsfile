def builders = [:]

def branchName = "${env.BRANCH_NAME}"

node('jenkins-slave-generic') {
    stage('build') {
        builders['linux-x86_64'] = {
            node('jenkins-slave-ec2') {
                env.PATH = "/usr/local/bin:${env.HOME}/.cargo/bin:${env.PATH}"

                stage('Bootstrap linux') {
                    sh "rustup update"
                }

                stage('Checkout linux') {
                    deleteDir()
                    checkout scm
                }

                stage('Build linux') {
                    sh "cargo build -v --all"
                }

                stage('Tests linux') {
                    sh "RUST_BACKTRACE=1 cargo test -v --all"
                }
            }
        }

        builders['macOS'] = {
            node('michel') {
                env.PATH = "/usr/local/bin:${env.HOME}/.cargo/bin:${env.PATH}"

                stage('Bootstrap macOS') {
                    sh "rustup update"
                }

                stage('Checkout macOS') {
                    deleteDir()
                    checkout scm
                }

                stage('Build macOS') {
                    sh "cargo build -v --all"
                }

                stage('Tests macOS') {
                    sh "RUST_BACKTRACE=1 cargo test -vv --all"
                }
            }
        }

        builders['rpi-x-compile'] = {
            node('jenkins-slave-ec2') {
                env.PATH = "/usr/local/bin:${env.HOME}/.cargo/bin:${env.PATH}"

                def toolchain = "/opt/pitools/arm-bcm2708/arm-rpi-4.9.3-linux-gnueabihf"
                def cc_conf = "TARGET_CC=${toolchain}/bin/arm-linux-gnueabihf-gcc " +
                    "TARGET_SYSROOT=${toolchain}/arm-linux-gnueabihf/sysroot " +
                    "CPATH=${toolchain}/lib/gcc/arm-linux-gnueabihf/4.9.3/include:${toolchain}/lib/gcc/arm-linux-gnueabihf/4.9.3/include-fixed"

                stage('Bootstrap rpi') {
                    sh "rustup update"
                }

                stage('Checkout rpi') {
                    deleteDir()
                    checkout scm
                }

                stage('Build rpi') {
                    sh "${cc_conf} cargo dinghy build -v"
                }

                stage('Tests rpi') {
                    sh "RUST_BACKTRACE=1 ${cc_conf} cargo dinghy test"
                }
            }
        }

        builders['android-arm'] = {
            node('jenkins-slave-ec2') {
                env.PATH = "/usr/local/bin:${env.HOME}/.cargo/bin:${env.PATH}"

                def toolchain = "/opt/android-toolchains/arm"
                def cc_conf = "TARGET_CC=${toolchain}/bin/arm-linux-androideabi-gcc " +
                              "TARGET_AR=${toolchain}/bin/arm-linux-androideabi-ar " +
                              "TARGET_SYSROOT=${toolchain}/sysroot " +
                              "CPATH=${toolchain}/lib/gcc/arm-linux-androideabi/4.9.x/include:" +
                                    "${toolchain}/lib/gcc/arm-linux-androideabi/4.9.x/include-fixed"

                stage('Bootstrap android') {
                    sh "rustup update"
                }

                stage('Checkout android') {
                    deleteDir()
                    checkout scm
                }

                stage('Build android') {
                    sh "${cc_conf} cargo build --target armv7-linux-androideabi"
                }
            }
        }

        parallel builders
    }
}
