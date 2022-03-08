project('rrstitcher', ['c','cpp'], default_options: ['warning_level=2'],
	version : '0.8.4',
	meson_version : '>= 0.54',)

project_name = meson.project_name()
project_version = meson.project_version()
version_arr = project_version.split('.')
version_major = version_arr[0].to_int()
version_minor = version_arr[1].to_int()
version_micro = version_arr[2].to_int()
if version_arr.length() == 4
  version_nano = version_arr[3].to_int()
else
  version_nano = 0
endif
version_is_dev = version_minor % 2 == 1 and version_micro < 90

# Define header directories
configinc = include_directories('.')

# Define compiler args and include directories
c_args = ['-DHAVE_CONFIG_H']
cpp_args = ['-DHAVE_CONFIG_H']

# Define installation directories
lib_install_dir = get_option('libdir')
plugins_pkgconfig_install_dir = join_paths(get_option('libdir'), 'pkgconfig')

# Get an object returns describing a compiler
cc = meson.get_compiler('c')
cxx = meson.get_compiler('cpp')

# Verify if the warning flags are available in the compiler
# If the flags is availale for the compiler it wiil be used in all compiler
# invocations with the exception of compile tests.
warning_flags = [
  '-Werror',
]
foreach extra_arg : warning_flags
  if cc.has_argument (extra_arg)
    # Add flag to the compiler command line
    add_project_arguments([extra_arg], language: 'c')
  endif
endforeach

cxx_flags = [
  '-Werror',
]
foreach extra_arg : cxx_flags
  if cxx.has_argument (extra_arg)
    # Add flag to the compiler command line
    add_project_arguments([extra_arg], language: 'cpp')
  endif
endforeach

# Create an empty configuration object to set config.h information
cdata = configuration_data()

# Plugin information
origin_url = 'https://ridgerun.com/'


# Set config.h information
cdata.set_quoted('LOCALEDIR', join_paths(get_option('prefix'), get_option('localedir')))
cdata.set_quoted('LIBDIR', join_paths(get_option('prefix'), get_option('libdir')))
cdata.set_quoted('PACKAGE', project_name)
cdata.set_quoted('PACKAGE_NAME', project_name)
cdata.set_quoted('PACKAGE_STRING', '@0@ @1@'.format(project_name,project_version))
cdata.set_quoted('PACKAGE_TARNAME', project_name)
cdata.set_quoted('PACKAGE_BUGREPORT', ' rrstitcherbugreport')
cdata.set_quoted('RR_PACKAGE_ORIGIN', origin_url)
cdata.set_quoted('PACKAGE_VERSION', project_version)
cdata.set_quoted('VERSION', project_version)

# These are only needed/used by the ABI tests
host_defines = [
  [ 'x86', 'HAVE_CPU_I386' ],
  [ 'x86_64', 'HAVE_CPU_X86_64' ],
  [ 'arm', 'HAVE_CPU_ARM' ],
  [ 'aarch64', 'HAVE_CPU_AARCH64' ],
  [ 'mips', 'HAVE_CPU_MIPS' ],
  [ 'powerpc', 'HAVE_CPU_PPC' ],
  [ 'powerpc64', 'HAVE_CPU_PPC64' ],
  [ 'alpha', 'HAVE_CPU_ALPHA' ],
  [ 'sparc', 'HAVE_CPU_SPARC' ],
  [ 'ia64', 'HAVE_CPU_IA64' ],
  [ 'hppa', 'HAVE_CPU_HPPA' ],
  [ 'm68k', 'HAVE_CPU_M68K' ],
  [ 's390', 'HAVE_CPU_S390' ],
]
foreach h : host_defines
  if h.get(0) == host_machine.cpu()
    cdata.set(h.get(1), 1)
  else
    cdata.set(h.get(1), false)
  endif
endforeach

cdata.set_quoted('HOST_CPU', host_machine.cpu())

# Imports pkgconfig module
pkgconfig = import('pkgconfig')

# Install git hooks
python3 = import('python').find_installation()
run_command(python3, '-c', 'import shutil; shutil.copy("common/hooks/pre-commit-hook.sh", ".git/hooks/pre-commit")')

# Meson will generate a header file all the entries in the configuration data object
configure_file(output : 'config.h', configuration : cdata)

executable('example', 'main.c')

