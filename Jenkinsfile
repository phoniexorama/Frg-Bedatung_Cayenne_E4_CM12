@Library('cm_shared_library') _
pipeline {
    agent {
        label 'CarMakerServer' // Label for Windows agent
    }
    environment {
        TEMPLATE_FOLDER_PATH = "${WORKSPACE}\\Data\\TestRun\\Lenkwinkelrampe_Template"
        TEMPLATE_FILE = "${WORKSPACE}\\Template.ts"
        DESTINATION_FOLDER = "${WORKSPACE}\\Data\\TestRun"
        VEHICLE_FOLDER_PATH = "${WORKSPACE}\\Data\\Vehicle"
        OUTPUT_FOLDER = "${WORKSPACE}\\SimOutput\\ENGPMAKNB022"
        LOG_FOLDER = "${WORKSPACE}\\SimOutput\\ENGPMAKNB022\\Log"
        VFF_FOLDER_PATH = "${WORKSPACE}\\Data\\Vehicle"
        DAT_FOLDER_PATH = "${WORKSPACE}\\SimOutput\\ENGPMAKNB022\\log"
        EXCEL_FOLDER_PATH = "${WORKSPACE}\\VehicleInfoExcel"
        BATCH_SCRIPT_PATH = "${WORKSPACE}\\carmakerTestseries.bat"
        TEST_SERIES_FOLDER_PATH = "${WORKSPACE}\\Data\\TestRun"
        FORMAT_FILE_CONFIG_PATH = "${WORKSPACE}\\Data\\Config\\Lenkwinkelrampe_Temp"
    }

    stages {
        
        stage('Dat File Generator') {
            steps {
                script {
                    // Call the Python script for dat file generation
                    //bat "python carmakerdatfilegenerator.py"
                    stepCarMakerDatFileGenerator()
                }
            }
        }

        stage('Excelfile Generator') {
            steps {
                script {
                    // Call the Python script for excel file generation
                    //bat "python autoexcelfilegenerator.py"
                    stepAutoExcelFileGenerator()
                }
            }
        }

        stage('Testseries Generator') {
            steps {
                script {
                    // Call the Python script for test series generation
                    //bat "python testseriesgenerator.py"
                    def fileName = 'Template.ts'
                    def sourcePath = 'template/Template.ts'
                    def targetPath = "${env.WORKSPACE}" // Use Jenkins workspace as target
                    
                    // Call the copyFile function to copy the file
                    stepCopyFile(fileName, sourcePath, targetPath)

                    stepTestSeriesGenerator()
                }
            }
        }
        
        stage('Run TestManager') {
            steps {
                script {
                    // Call the Python script for running test manager
                    //bat "python runtestmanager.py"
                    def fileName = 'carmakerTestseries.bat'
                    def sourcePath = 'bat/carmakerTestseries.bat'
                    def targetPath = "${env.WORKSPACE}" // Use Jenkins workspace as target
                    def tclFile = 'CMGUI_RemCtrl.tcl'
                    def tclSourcePath = 'tcl/CMGUI_RemCtrl.tcl'
                    def tclDesPath = "${env.WORKSPACE}\\Data\\Script\\Examples"
                    
                    // Call the copyFile function to copy the file
                    stepCopyFile(fileName, sourcePath, targetPath)
                    
                    // Call the copyFile function to copy the tcl file
                    stepCopyFile(tclFile, tclSourcePath, tclDesPath)
                    
                    stepRunTestManager()
                }
            }
        }

        stage('Pack Artifacts') {
            steps {
                script {

                   // Get the WORKSPACE environment variable
                    def workspace = env.WORKSPACE
                    
                    // Define the last name from the workspace path
                    def lastIndex = workspace.lastIndexOf('\\')
                    def lastName = workspace.substring(lastIndex + 1)
                    println "Last Name: ${lastName}"
                    
                    // Define main directory path based on last name
                    def mainDirectoryPath = "${workspace}\\${lastName}"
                    
                    // Check and create main directory if not exists
                    bat "mkdir \"${mainDirectoryPath}\""
                    
                    // Define the Data directory path
                    def dataDirectoryPath = "${mainDirectoryPath}\\Data"
                    
                    // Check and create Data directory if not exists
                    bat "mkdir \"${dataDirectoryPath}\""
                    
                    // Define source paths and corresponding destination paths
                    def sourceDestinationMappings = [
                        "${workspace}\\Data\\TestRun": "${dataDirectoryPath}\\TestRun",
                        "${workspace}\\Data\\Chassis": "${dataDirectoryPath}\\Chassis",
                        "${workspace}\\Data\\Tire": "${dataDirectoryPath}\\Tire",
                        "${workspace}\\Data\\Pic": "${dataDirectoryPath}\\Pic",
                        "${workspace}\\Data\\Vehicle": "${dataDirectoryPath}\\Vehicle",
                        "${workspace}\\Movie": "${mainDirectoryPath}\\Movie"
                    ]
                                    
                    // Copy folders with recursive content from source paths to corresponding destination paths
                    stepCopyFolder(sourceDestinationMappings)
                }
            }
        }    
    }
}
