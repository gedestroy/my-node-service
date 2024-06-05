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
                                    platform: [version: '03.06.14.00'],
                                    eagle   : [version: '02.01.16.00'],
                                    'data-fis-17': [version: '00.35.09.08'],
                                    'data-fis-3': [version: '00.35.09.10'],
                                    prettier: [version: '3.3.1'],
                                    eslint: [version: '9.4.0']
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
