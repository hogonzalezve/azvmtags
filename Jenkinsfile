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
        string(name: 'VM_LIST', defaultValue: '', description: 'Comma-separated list of VM names. Ref: vm1,vm2,vm3')
        string(name: 'ADD_VM_GROUP_START_TAG_KEY', defaultValue: '', description: 'Key for the start group tag. Ref: grupo1start, grupo2start ')
        string(name: 'ADD_VM_GROUP_STOP_TAG_KEY', defaultValue: '', description: 'Key for the stop group tag. Ref: grupo1stop, grupo2stop')
        string(name: 'REMOVE_VM_GROUP_START_TAG_KEY', defaultValue: '', description: 'Key for the start tag group to remove. Ref: grupo1start, grupo2start')
        string(name: 'REMOVE_VM_GROUP_STOP_TAG_KEY', defaultValue: '', description: 'Key for the stop tag group to remove. Ref: grupo1stop, grupo2stop')
    }

    stages {
        stage('deploy') {
            steps {
                script {
                        sh '''
                            az login --service-principal -u 9e8b8c0e-0f92-4325-b421-2028bf37b447 -p aE~8Q~O8K_Vl_bhrS2YoAPGzILK7r3Bd51E52dc3 -t 9dbc76ea-fb25-4b07-8f07-5dc315999b76
                            az account set -s 0bea0a37-89cb-43fb-976f-0d8a3d8b1e4b
                        '''
                    
                        def vmList = params.VM_LIST.split(',')
                        def startTagValue = 'start'
                        def stopTagValue = 'stop'
                    
                        if (params.ADD_VM_GROUP_START_TAG_KEY && params.ADD_VM_GROUP_STOP_TAG_KEY) {
                            updateVMTags(vmList, params.ADD_VM_GROUP_START_TAG_KEY, startTagValue, params.ADD_VM_GROUP_STOP_TAG_KEY, stopTagValue)
                        }
                        if (params.REMOVE_VM_GROUP_START_TAG_KEY || params.REMOVE_VM_GROUP_STOP_TAG_KEY) {
                            removeVMTag(vmList, params.REMOVE_VM_GROUP_START_TAG_KEY, params.REMOVE_VM_GROUP_STOP_TAG_KEY)
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
    def resourceGroup = 'rg_occidente_temp'

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
 * @param deleteStartTagKey The key for the start tag to remove
 * @param deleteStopTagKey The key for the stop tag to remove
 */
def removeVMTag(vmList, deleteStartTagKey, deleteStopTagKey) {
    def resourceGroup = 'rg_occidente_temp'

    vmList.each { vmName ->
        if (deleteStartTagKey) {
            sh """
                az vm update --resource-group ${resourceGroup} --name ${vmName} --remove tags.${deleteStartTagKey}
            """
        }
        if (deleteStopTagKey) {
            sh """
                az vm update --resource-group ${resourceGroup} --name ${vmName} --remove tags.${deleteStopTagKey}
            """
        }
    }
}
