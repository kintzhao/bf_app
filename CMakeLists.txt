cmake_minimum_required(VERSION 2.8.3)
project(bf_app)

# bfl (Bayesian Filtering Library) is a third party package that uses pkg-config
find_package(PkgConfig)
pkg_check_modules(BFL REQUIRED orocos-bfl)

include_directories(${BFL_INCLUDE_DIRS})
link_directories(${BFL_LIBRARY_DIRS})

find_package(catkin REQUIRED
        COMPONENTS
            roscpp
            tf
            nav_msgs
            std_msgs
            geometry_msgs
            sensor_msgs
        )

find_package(Boost REQUIRED COMPONENTS thread)

catkin_package(
    CATKIN_DEPENDS
        geometry_msgs
        nav_msgs
        roscpp
        sensor_msgs
        std_msgs
)

include_directories(
    "include"
    "include/bf_demo"
    ${catkin_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIRS}
    )

############################ robot_pose_ekf
add_executable(robot_pose_ekf 
                       src/robot_pose_ekf/odom_estimation.cpp 
                       src/robot_pose_ekf/nonlinearanalyticconditionalgaussianodo.cpp 
                       src/robot_pose_ekf/odom_estimation_node.cpp)
target_link_libraries(robot_pose_ekf ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${BFL_LIBRARIES} )
add_dependencies(robot_pose_ekf ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})


############################ bf_demo
add_library(mobile_robot ./src/bf_demo/nonlinearanalyticconditionalgaussianmobile.cpp ./src/bf_demo/mobile_robot.cpp  )
target_link_libraries(mobile_robot ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${BFL_LIBRARIES} )

ADD_EXECUTABLE( test_linear_kalman  ./src/bf_demo/linear_kalman/test_linear_kalman.cpp ) 
target_link_libraries(test_linear_kalman ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${BFL_LIBRARIES} mobile_robot)
add_dependencies(test_linear_kalman ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

ADD_EXECUTABLE( test_compare_filters  ./src/bf_demo/compare_filters/test_compare_filters.cpp) 
target_link_libraries(test_compare_filters ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${BFL_LIBRARIES} mobile_robot)
add_dependencies(test_compare_filters ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

ADD_EXECUTABLE( test_nonlinear_kalman  ./src/bf_demo/nonlinear_kalman/test_nonlinear_kalman.cpp) 
target_link_libraries(test_nonlinear_kalman ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${BFL_LIBRARIES} mobile_robot)
add_dependencies(test_nonlinear_kalman ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})


ADD_EXECUTABLE( test_nonlinear_particle   ./src/bf_demo/nonlinear_particle/test_nonlinear_particle.cpp
                                        ./src/bf_demo/nonlinear_particle/nonlinearMeasurementPdf.cpp
                                        ./src/bf_demo/nonlinear_particle/nonlinearSystemPdf.cpp
) 
target_link_libraries(test_nonlinear_particle ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${BFL_LIBRARIES} mobile_robot)
add_dependencies(test_nonlinear_particle ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})


ADD_EXECUTABLE( test_nonlinear_smoother  ./src/bf_demo/smoother/test_nonlinear_smoother.cpp
) 
target_link_libraries(test_nonlinear_smoother ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${BFL_LIBRARIES} mobile_robot)
add_dependencies(test_nonlinear_smoother ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
 
ADD_EXECUTABLE( test_kalman_smoother  ./src/bf_demo/smoother/test_kalman_smoother.cpp
) 
target_link_libraries(test_kalman_smoother ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${BFL_LIBRARIES} mobile_robot)
add_dependencies(test_kalman_smoother ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})


ADD_EXECUTABLE( test_discrete_filter  ./src/bf_demo/discrete_filter/test_discrete_filter.cpp 
                                    ./src/bf_demo/discrete_filter/conditionalUniformMeasPdf1d.cpp) 
target_link_libraries(test_discrete_filter ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${BFL_LIBRARIES} mobile_robot)
add_dependencies(test_discrete_filter ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})



install( TARGETS        robot_pose_ekf test_linear_kalman test_nonlinear_kalman test_nonlinear_particle 
        test_nonlinear_smoother test_kalman_smoother
    DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  FILES_MATCHING PATTERN "*.launch")


