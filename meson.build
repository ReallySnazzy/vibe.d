project('Vibe.d', 'd',
    meson_version: '>=0.40',
    subproject_dir: 'lib/subprojects',
    license: 'MIT',
    version: '0.9.5'
)

project_soversion      = '0'
project_version_suffix = ''
project_version        = meson.project_version()
project_version_full   = project_version + project_version_suffix

pkgc = import('pkgconfig')

#
# Dependencies
#
zlib_dep = dependency('zlib')
crypto_dep = dependency('libcrypto')
ssl_dep = dependency('libssl')

#
# Compiler flags
#
flag_new_openssl_ldc = []
flag_new_openssl_dmd = []

if meson.is_subproject() == false
    if meson.get_compiler('d').get_id() == 'llvm'
        add_global_arguments(['-d-version=Have_openssl',
                              '-d-version=Have_diet_ng',
                              '-d-version=Have_stdx_allocator',
                              flag_new_openssl_ldc], language : 'd')
    endif
    if meson.get_compiler('d').get_id() == 'dmd'
        add_global_arguments(['-version=Have_openssl',
                              '-version=Have_diet_ng',
                              '-version=Have_stdx_allocator',
                              flag_new_openssl_dmd], language : 'd')
    endif
endif

if meson.get_compiler('d').get_id() == 'gnu'
    error('Vibe.d can not be compiled with GDC at time (2016). Sorry.')
endif

#
# D dependencies
#

# we need to search for this dependency after setting global compiler flags, because
# it may pull in a subproject after which setting global flags is not allowed anymore
diet_dep = dependency('diet', fallback: ['diet', 'diet_dep'])
allocator_dep = dependency('stdx-allocator', fallback: ['stdx-allocator', 'allocator_dep'])

# directory where the external dependencies are included from.
# Meson will search for this dir in both build_root and source_root
subproject_dir = 'lib/subprojects'

# Try to find system OpenSSL bindings, if not found, download
# a Git copy.
openssl_src_dir = ''
if run_command('[', '-d', '/usr/include/d/common/deimos/openssl/', ']').returncode() == 0
    openssl_src_dir = '/usr/include/d/common'
else
    openssl_src_dir = subproject_dir + '/openssl'
    if run_command('[', '-d', openssl_src_dir, ']').returncode() != 0
        message('Fetching OpenSSL D bindings from Github...')
        git_get_requests = run_command(['git', 'clone', 'https://github.com/s-ludwig/openssl.git', openssl_src_dir])
        if git_get_requests.returncode() != 0
            error('Unable to fetch OpenSSL bindings.\n' + git_get_requests.stderr())
        endif
    endif

    message('Using non-system OpenSSL D bindings.')
endif
openssl_inc = include_directories(openssl_src_dir)

#
# Modules
#

# Utils
subdir('utils/')

# Data
subdir('data/')

# Crypto
subdir('crypto/')

# Stream
subdir('stream/')

# TextFilter
subdir('textfilter/')

# INet
subdir('inet/')

# TLS
subdir('tls/')

# HTTP
subdir('http/')

# Mail
subdir('mail/')

# MongoDB
subdir('mongodb/')

# Redis
subdir('redis/')

# Web
subdir('web/')
