cmake_minimum_required(VERSION 3.0)

# Fetch the correct verion of the max-api
message(STATUS "Updating Git Submodules")
execute_process(
	COMMAND				git submodule update --init --recursive
	WORKING_DIRECTORY	"${CMAKE_CURRENT_SOURCE_DIR}"
)


# Misc setup and subroutines
include(${CMAKE_CURRENT_SOURCE_DIR}/source/max-api/script/max-package.cmake)

# User-defined projects folder

set(PROJECTS_FOLDER ${CMAKE_CURRENT_SOURCE_DIR}/source/projects)
if(DEFINED USER_PROJECTS_FOLDER AND IS_DIRECTORY ${USER_PROJECTS_FOLDER})
	set(PROJECTS_FOLDER ${USER_PROJECTS_FOLDER})

	# Set C74_MAX_API_DIR that max-[pre|post]target.cmake can find it
	if(NOT DEFINED C74_MAX_API_DIR)
		set(C74_MAX_API_DIR ${CMAKE_CURRENT_LIST_DIR}/source/max-api)
	endif()
	# Set output directory for externals
	if(NOT DEFINED C74_LIBRARY_OUTPUT_DIRECTORY)
		set(C74_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/externals)
	endif()

	message(STATUS "USER_PROJECTS_FOLDER=${USER_PROJECTS_FOLDER}")
	message(STATUS "C74_LIBRARY_OUTPUT_DIRECTORY=${C74_LIBRARY_OUTPUT_DIRECTORY}")
endif()


# Generate a project for every folder in the "source/projects" folder
SUBDIRLIST(PROJECT_DIRS ${PROJECTS_FOLDER})
foreach (project_dir ${PROJECT_DIRS})
	if (EXISTS "${PROJECTS_FOLDER}/${project_dir}/CMakeLists.txt")
		message("Generating: ${project_dir}")
		add_subdirectory(${PROJECTS_FOLDER}/${project_dir} ${PROJECTS_FOLDER}/${project_dir}/build)
	endif ()
endforeach ()
