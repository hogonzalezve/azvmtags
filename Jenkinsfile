#!/usr/bin/env groovy
/* Only keep the 10 most recent builds. */

def projectProperties = [
    buildDiscarder(logRotator(numToKeepStr: '10')),
]

properties(projectProperties)

/* Librería manejo de variables */
import groovy.transform.Field

/* Creación Variables Globales */
@Field def VARIABLE_NAME = ''

pipeline {
    agent {
        label 'principal'
    }

    options {
        timeout(time: 15, unit: 'MINUTES')
    }

    parameters {
        string(name: 'VM_LIST', defaultValue: 'vm1,vm2,vm3', description: 'Comma-separated list of VM names')
        string(name: 'START_TAG_KEY', defaultValue: 'grupo1start', description: 'Key for the start tag')
        string(name: 'START_TAG_VALUE', defaultValue: 'start', description: 'Value for the start tag')
        string(name: 'STOP_TAG_KEY', defaultValue: 'grupo1stop', description: 'Key for the stop tag')
        string(name: 'STOP_TAG_VALUE', defaultValue: 'stop', description: 'Value for the stop tag')
        string(name: 'REMOVE_TAG_KEY', defaultValue: '', description: 'Key for the tag to remove')
    }

    stages {
        stage('deploy') {
            steps {
                script {
                    def vmList = params.VM_LIST.split(',')
                    updateVMTags(vmList, params.START_TAG_KEY, params.START_TAG_VALUE, params.STOP_TAG_KEY, params.STOP_TAG_VALUE)
                    if (params.REMOVE_TAG_KEY) {
                        removeVMTag(vmList, params.REMOVE_TAG_KEY)
                    }
                }
            }
        }
    }
}

/**
 * Function to update VM tags
 * @param vmList The list of VM names
 * @param startTagKey The key for the start tag
 * @param startTagValue The value for the start tag
 * @param stopTagKey The key for the stop tag
 * @param stopTagValue The value for the stop tag
 */
def updateVMTags(vmList, startTagKey, startTagValue, stopTagKey, stopTagValue) {
    def resourceGroup = 'rpa-rg-prod'

    vmList.each { vmName ->
        sh """
            az vm update --resource-group ${resourceGroup} --name ${vmName} --set tags.${startTagKey}=${startTagValue}
            az vm update --resource-group ${resourceGroup} --name ${vmName} --set tags.${stopTagKey}=${stopTagValue}
        """
    }
}

/**
 * Function to remove a specific VM tag
 * @param vmList The list of VM names
 * @param tagKey The key for the tag to remove
 */
def removeVMTag(vmList, tagKey) {
    def resourceGroup = 'rpa-rg-prod'

    vmList.each { vmName ->
        sh """
            az vm update --resource-group ${resourceGroup} --name ${vmName} --remove tags.${tagKey}
        """
    }
}
