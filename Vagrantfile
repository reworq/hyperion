# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANT_BUILD_FOLDER = "/tmp/build_folder"
VAGRANT_SYNCED_FOLDER = "/tmp/synced_folder"

$mingw_script = <<-'SHELL'
  echo "Updating/installing base dependencies..."
  sudo apt-get update
  sudo apt-get install -y git tree wget

  echo "Installing latest CMake..."
  CMAKE_URL="https://github.com/Kitware/CMake/releases/download/v3.19.4/cmake-3.19.4-Linux-x86_64.sh"
  wget -O /tmp/cmake.sh -nv $CMAKE_URL
  sudo sh /tmp/cmake.sh --prefix="/usr" --skip-license

  echo "Installing MinGW..."
  sudo apt-get install -y mingw-w64 mingw-w64-common

  echo "Creating build directory #{VAGRANT_BUILD_FOLDER}"
  mkdir "#{VAGRANT_BUILD_FOLDER}"
SHELL

Vagrant.configure("2") do |config|
  config.vm.synced_folder ".", "#{VAGRANT_SYNCED_FOLDER}"

  config.vm.define "mingw32" do |mingw32|
    mingw32.vm.hostname = "mingw32"
    mingw32.vm.box = "bento/ubuntu-16.04"
    mingw32.vm.box_check_update = false

    mingw32.vm.provision "shell", run: "once", privileged: false, inline: $mingw_script
  end

  config.vm.define "mingw64" do |mingw64|
    mingw64.vm.hostname = "mingw64"
    mingw64.vm.box = "bento/ubuntu-16.04"
    mingw64.vm.box_check_update = false

    mingw64.vm.provision "shell", run: "once", privileged: false, inline: $mingw_script
  end

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.cpus = 1
    vb.memory = "1024"
  end
end
