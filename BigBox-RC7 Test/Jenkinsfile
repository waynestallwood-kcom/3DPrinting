node('arduino') {
	stage 'Checkout'
	checkout scm

	stage 'Load and prepare environment'
	sh '''wget -O arduino-1.6.4-linux64.tar.xz ftp://nas01.alitecs.local/Jenkins/arduino-1.6.4-linux64.tar.xz
wget -O generate_version_header_for_marlin ftp://nas01.alitecs.local/Jenkins/generate_version_header_for_marlin
tar Jxf arduino-1.6.4-linux64.tar.xz
chmod +x generate_version_header_for_marlin
'''

	stage 'Prepare dummy Xserver'
	sh '''daemonize -E BUILD_ID=dontKillMe /usr/bin/Xvfb :1 -screen 0 1024x768x16
DISPLAY=:1.0'''

	stage 'Install additional Arduino libraries'
	sh '''export DISPLAY=:1.0
./arduino-1.6.4/arduino --install-library "U8glib,LiquidCrystal"'''

	stage 'Build'
	sh '''export DISPLAY=:1.0
./generate_version_header_for_marlin ./Marlin
./arduino-1.6.4/arduino --verify --board arduino:avr:mega:cpu=atmega2560 Marlin/Marlin.ino \\
--pref build.path=./buildroot'''

	stage 'Archive'
	sh '''mv buildroot/Marlin.cpp.hex buildroot/Marlin.${JOB_BASE_NAME##*%2F}.hex'''

	archive 'buildroot/Marlin.*.hex'

	stage 'Clean'
	deleteDir()
}