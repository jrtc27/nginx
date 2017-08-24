properties([
    disableConcurrentBuilds(),
    pipelineTriggers([]),
])

stage("Build") {
    def before = {
        sh '''
            git clone https://github.com/nginx/nginx-tests.git || git -C nginx-tests pull --rebase
            apt-get install -y --no-install-recommends git wget
            rm -rf tmp && mkdir -p tmp/nginx-tests
            cp -aR nginx-tests/* tmp/nginx-tests
            tar cJf nginx-tests.tar.xz tmp
            # TODO: use perl from pkg.FreeBSD.org? (for some reason can't extract it properly though)
            wget https://transfer.sh/AmikH/perl-mips-junit.tar.xz
            ls -la
        '''
    }
    cheribuildProject(name:'nginx', extraArgs: '--with-libstatcounters --nginx/no-debug-info',
                      targets: ['mips'],  // only run one for now
                      testScript: 'cd /opt/$CPU/ && sh -xe ./run-nginx-tests.sh',
                      beforeTests: before, testExtraArgs: '--test-archive nginx-tests.tar.xz --test-archive perl-mips-junit.tar.xz' 
                      /* minimalTestImage: true */) // TODO: once we need perl this will no longer be true
}