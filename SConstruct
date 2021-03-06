#******************************************************************
#
#
#-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
#-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

##
# The main build script
#
##
import os

# Load common build config
SConscript('build_common/SConscript')

# The construction environment named 'env' is set up in build_common
# and should be imported by all other scripts which need access to it.
# Scripts which do not need to modify the global construction environment
# should Clone() this environment and modify the clone.
# Scripts which need to modify the global environment should not
# work on a clone, since those changes will not propagate.
# Normally global changes should be limited to build_common and
# the extlibs scripts.
Import('env')

if os.environ.get('TERM') is not None:
    env['ENV']['TERM'] = os.environ['TERM']

# Load extra options
SConscript('extra_options.scons')

target_os = env.get('TARGET_OS')
if target_os == 'arduino':
    SConscript('arduino.scons')

# By default, src_dir is the current dir, build_dir is:
#     ./out/<target_os>/<target_arch>/<release or debug>/
#
# The build_dir is a Variant directory of the source directory.
# iotivity variant directories are set up with argument "duplicate=0",
# which means build_dir files will behave like soft links to src_dir files.
# For more reading on this:
#     http://www.scons.org/doc/production/HTML/scons-user.html#f-VariantDir
#
# Any way, to make the output is in build_dir, when load scripts, the path should
# be relevant to build_dir.
build_dir = env.get('BUILD_DIR')

# Build 'resource' sub-project
SConscript(build_dir + 'resource/SConscript')

if target_os not in ['arduino','darwin','ios', 'android', 'msys_nt', 'windows']:
    SConscript(build_dir + 'examples/OICMiddle/SConscript')
    if env.get('SECURED') == '1':
        SConscript(build_dir + 'examples/OCFSecure/SConscript')
        SConscript(build_dir + 'examples/OCFDeviceBuilder/SConscript')
        SConscript(build_dir + 'examples/OCFArtikEnviro/SConscript')
        SConscript(build_dir + 'examples/OCFArtikLED/SConscript')

java_build = None
if (env.get('BUILD_JAVA') and env.get('JAVA_HOME')) or target_os == 'android':
    java_build = SConscript(build_dir + 'java/SConscript')

# Build 'service' sub-project
service_build = SConscript(build_dir + 'service/SConscript')

if java_build:
    Depends(service_build, java_build)

# Build other sub-projects
SConscript(dirs=[
    build_dir + 'cloud',
    build_dir + 'plugins',
    build_dir + 'bridging',
])

# Append target information to the help information (if needed)
# To see help info, execute:
#     $ scons [options] -h
# Note some help is option-dependent, e.g. java-related options are
# not added to the help unless BUILD_JAVA is seen
#
# This is not really needed unless requesting help, consider adding check:
#if env.GetOption('help'):
env.PrintTargets()

# Print bin upload command line (arduino only)
if target_os == 'arduino':
    env.UploadHelp()

# to install the generated pc file into custom prefix location
env.UserInstallTargetPCFile('iotivity.pc', 'iotivity.pc')
