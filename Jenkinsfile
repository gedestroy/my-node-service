serviceBuildPipeline([
        projectName    : 'hooli',
        properties     : [
                parameters([
                        releaseParameter()
                ])
        ],
        containerConfig: [
                cypress : [version: 'node12.6.0-chrome77']
        ]
]) { jslPipeline ->
    jslPipeline
            .checkout()
            .setBranchVersion()
            .buildMaven([spotless: true, slim: true])
            .runKarma()
            .withInstances([instances: [
                    vehicleInstance: [
                            services: [
                                    platform: [version: '%release%'],
                                    eagle   : [version: '%release%'],
                                    'data-fis-17': [version: '00.35.09.08'],
                                    'data-fis-3': [version: '00.35.09.10'],
                                    hooli: [version: '%currentBranch%']
                            ]
                    ]
            ]
            ]) { withInstanceState ->
                withInstanceState.executeSelenium([
                        testTargetInstance: 'vehicleInstance',
                        testStartPage     : 'services/hooli/index.html'
                ])
                withInstanceState.aggregateCodeCoverage()
            }
            .finalizeAndUpdateVersion()
            .saveArtifacts()
            .executeSonar()
            .sendEmail([
                    emailRecipients : 'pascal.keusch@postauto.ch'
            ])
}
