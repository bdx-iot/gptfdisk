project('gptfdisk',
    'cpp',
    version: '1.0.5',
    license: 'GPLv2',
)

cc = meson.get_compiler('cpp')
config = configuration_data()
config.set('PACKAGE_VERSION', meson.project_version())

curses_dep = dependency(
    'ncurses',
    required: true
)

popt_dep = dependency(
    'popt',
    required: true
)

uuid_dep = dependency(
    'uuid',
    required: true
)

if cc.has_header('ncursesw/ncurses.h', dependencies: curses_dep)
    config.set('HAVE_NCURSESW_NCURSES_H', true)
elif cc.has_header('ncurses.h', dependencies: curses_dep)
    config.set('HAVE_NCURSES_H', true)
else
    error('ncursesw found, but no header found')
endif

configure_file(
    input: 'config.h.in',
    output: 'config.h',
    configuration: config
)

add_global_arguments('-DHAVE_CONFIG_H',
    language: 'cpp')

lib_files = files(
    'crc32.cc',
    'support.cc',
    'guid.cc',
    'gptpart.cc',
    'mbrpart.cc',
    'basicmbr.cc',
    'mbr.cc',
    'gpt.cc',
    'bsd.cc',
    'parttypes.cc',
    'attributes.cc',
    'diskio.cc',
    'diskio-unix.cc',
)

mbr_libs = files(
    'support.cc',
    'diskio.cc',
    'diskio-unix.cc',
    'basicmbr.cc',
    'mbrpart.cc',
)

sgdisk = executable(
    'sgdisk',
    [lib_files, 'sgdisk.cc', 'gptcl.cc'],
    dependencies : [ popt_dep, uuid_dep ],
    install: true
)

gdisk = executable(
    'gdisk',
    [lib_files, 'gdisk.cc', 'gptcurses.cc', 'gpttext.cc'],
    dependencies : [ curses_dep, uuid_dep ],
    install: true
)

cgdisk = executable(
    'cgdisk',
    [lib_files, 'gpttext.cc', 'gptcurses.cc', 'cgdisk.cc'],
    dependencies : [ curses_dep, uuid_dep ],
    install: true
)

fixparts = executable(
    'fixparts',
    [mbr_libs, 'fixparts.cc'],
    install: true
)
