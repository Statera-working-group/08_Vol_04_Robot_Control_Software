**Volume 04 Robot Control Software**


# 04. Steering Control Software

##  

## 04.01 Ackermann Steering Kinematics and SW Model [w/Code]

![](images/image1.png){width="7.268055555555556in" height="7.268055555555556in"}

Ackermann steering is a geometric steering principle used by wheeled vehicles and mobile robots to achieve smooth turning while minimizing lateral tire slip. During a turn, the inner and outer wheels travel along circles with different radii, so they cannot use identical steering angles. The Ackermann relationship determines the individual wheel angles required for their extended wheel axes to intersect near a common instantaneous center of rotation.

The basic kinematic model represents the vehicle using wheelbase, track width, steering angle, longitudinal velocity, and vehicle pose. Wheelbase is the distance between front and rear axle reference points, while track width describes the lateral separation between left and right wheels. These dimensions directly affect the steering geometry and must therefore be represented as calibrated parameters rather than fixed assumptions hidden inside the control software.

For an ideal front-wheel Ackermann configuration, the inner wheel requires a larger steering angle than the outer wheel. If the wheelbase is L, track width is W, and the turning radius measured from the rear axle center is R, the ideal relationships can be expressed as tan(delta_inner)=L/(R-W/2) and tan(delta_outer)=L/(R+W/2). These equations form the fundamental geometric conversion between requested curvature and physical wheel steering angles.

A convenient software abstraction is the bicycle model, which replaces the two front wheels with one equivalent steering wheel at the axle center. With equivalent steering angle delta, the path curvature can be approximated by kappa=tan(delta)/L and the corresponding turning radius by R=L/tan(delta). This simplification reduces computational complexity and is particularly useful for trajectory tracking, state estimation, simulation, and high-level motion-control interfaces.

The bicycle model describes planar vehicle motion through the vehicle position x and y and heading angle psi. Under low-speed kinematic assumptions, the state equations are commonly written as x_dot=v cos(psi), y_dot=v sin(psi), and psi_dot=v tan(delta)/L. A discrete software implementation evaluates these equations at a defined control period, allowing the predicted pose to be updated consistently with the real-time steering and velocity commands.

The software model should clearly distinguish path curvature, equivalent steering angle, road-wheel angle, steering actuator position, and steering-wheel or motor-side angle. These variables are related but are not interchangeable. Mechanical steering ratios, linkage geometry, actuator reduction ratios, offsets, and nonlinear characteristics can transform one representation into another, requiring explicit conversion functions between the planning, control, actuator, and feedback domains.

A practical steering software pipeline receives desired curvature or steering commands from the motion-control layer and first applies validity and range checks. The command is then converted into an equivalent center steering angle and subsequently into left and right Ackermann wheel angles. Steering limits, rate limits, acceleration constraints, and actuator-specific transformations are applied before target commands are transmitted to the steering motor or steering-by-wire controller.

The inverse transformation is equally important because measured steering positions must be converted back into a physically meaningful vehicle state. Sensor readings may represent motor encoder counts, actuator displacement, steering rack position, or individual wheel angles. Calibration parameters convert these measurements into normalized steering coordinates, after which the software can estimate equivalent steering angle, curvature, and instantaneous turning radius for feedback and diagnostics.

Real steering mechanisms rarely satisfy ideal Ackermann geometry across the entire operating range. Tie rods, steering arms, suspension movement, compliance, backlash, tire deformation, and manufacturing tolerances introduce nonlinear deviations. Production software can therefore supplement the analytical model with lookup tables, polynomial corrections, piecewise functions, or experimentally identified mappings while retaining the ideal kinematic equations as the reference model.

Steering angle saturation must be handled consistently because excessive requested curvature can generate physically impossible wheel commands. The software should determine allowable curvature from mechanical steering limits and vehicle geometry, then constrain commands before inverse kinematic conversion. Rate limiting is also necessary because an achievable steering angle does not imply that the steering actuator can reach that angle instantaneously during vehicle motion.

Low-speed Ackermann modeling assumes rolling without significant lateral slip, making it appropriate for many outdoor mobile robots, autonomous platforms, utility vehicles, and low-speed autonomous driving systems. As velocity increases, tire slip angles, lateral forces, load transfer, and transient dynamics become increasingly important. The software architecture should therefore define the operating region in which the kinematic model is valid and provide a clean interface for higher-fidelity dynamic models.

The sign convention used by the steering model must be defined unambiguously. A typical implementation assigns positive longitudinal velocity to forward motion, positive steering to a left turn, and positive yaw rate to counterclockwise rotation, but other conventions are possible. Coordinate-frame definitions, angle units, curvature signs, and wheel identifiers should be standardized across localization, planning, control, simulation, CAN communication, and diagnostic software.

Numerical robustness is important near the straight-driving condition because steering angle and curvature approach zero and the calculated turning radius approaches infinity. Software should avoid unnecessary division by very small curvature values and should use a defined straight-line threshold. Similar protection is required near steering limits, where tangent and inverse-tangent calculations can amplify numerical errors or produce discontinuities if ranges are not carefully constrained.

A maintainable implementation separates vehicle geometry from the kinematic algorithms. Parameters such as wheelbase, front and rear track width, maximum wheel angles, steering ratio, zero offset, and calibration coefficients should reside in a configuration or calibration structure. The kinematic functions can then operate on this parameter set, allowing the same software component to support multiple robot variants without modifying the core steering equations.

The steering model also provides useful diagnostic relationships. Given commanded steering, measured wheel angles, vehicle velocity, and estimated yaw rate, the software can compare expected and observed curvature. Persistent disagreement may indicate steering sensor offset, actuator tracking error, mechanical linkage problems, tire slip, or incorrect calibration. Residual thresholds and temporal filtering can convert these model inconsistencies into diagnostic information without coupling fault logic directly to actuator control.

Testing should begin with deterministic unit tests for straight driving, left and right turns, minimum-radius conditions, steering saturation, reverse motion, and near-zero numerical cases. Symmetry tests can verify that equal-magnitude left and right commands produce geometrically mirrored results. Parameter-boundary tests should confirm that invalid wheelbase, track width, steering limits, or non-finite command values are detected before they propagate into actuator commands.

Software-in-the-loop testing can connect the Ackermann model to trajectory generators and path-tracking controllers to verify curvature continuity and pose evolution. Hardware-in-the-loop testing extends validation to actual steering ECUs, motor controllers, sensors, communication delays, and actuator dynamics. Logged command and feedback signals can then be compared against the kinematic reference to identify latency, saturation, hysteresis, calibration error, and mechanical asymmetry.

In an integrated robot control architecture, Ackermann kinematics forms the mathematical boundary between vehicle-level motion intent and wheel-level steering actuation. The planner can operate primarily with trajectories and curvature, while the steering controller manages physical wheel angles and actuator commands. Maintaining this separation reduces dependencies between software layers and allows planners, controllers, steering hardware, and vehicle platforms to evolve independently.

A robust Ackermann steering software model therefore combines ideal geometry, explicit coordinate conventions, parameterized vehicle dimensions, actuator mappings, numerical protection, command constraints, feedback conversion, and model-based diagnostics. When these responsibilities are clearly separated, the same kinematic foundation can support simulation, real-time control, calibration, testing, and field diagnostics while providing a consistent interface for subsequent path-tracking and steering-safety functions.

:::

애커먼 조향(Ackermann Steering)은 바퀴가 장착된 차량과 이동 로봇(Wheeled Vehicle and Mobile Robot)이 선회할 때 횡방향 타이어 미끄러짐(Lateral Tire Slip)을 최소화하면서 부드럽게 회전하도록 하는 기하학적 조향 원리(Geometric Steering Principle)이다. 선회 중에는 내측 바퀴(Inner Wheel)와 외측 바퀴(Outer Wheel)가 서로 다른 반경의 원을 따라 이동하므로 동일한 조향각을 사용할 수 없다. 애커먼 관계(Ackermann Relationship)는 각 바퀴 축의 연장선이 공통의 순간 회전 중심(Instantaneous Center of Rotation) 부근에서 교차하도록 필요한 개별 바퀴 조향각을 결정한다.

기본 운동학 모델(Kinematic Model)은 휠베이스(Wheelbase), 윤거(Track Width), 조향각(Steering Angle), 종방향 속도(Longitudinal Velocity), 차량 자세(Vehicle Pose)를 이용하여 차량을 표현한다. 휠베이스는 전륜축(Front Axle)과 후륜축(Rear Axle)의 기준점 사이 거리이며, 윤거는 좌우 바퀴 사이의 횡방향 간격을 나타낸다. 이러한 차량 치수는 조향 기하학(Steering Geometry)에 직접 영향을 주기 때문에 제어 소프트웨어(Control Software) 내부에 고정된 가정값으로 숨기기보다는 보정 가능한 파라미터(Calibrated Parameter)로 표현해야 한다.

이상적인 전륜 애커먼 구성(Front-Wheel Ackermann Configuration)에서는 내측 바퀴가 외측 바퀴보다 더 큰 조향각을 필요로 한다. 휠베이스를 L, 윤거를 W, 후륜축 중심에서 측정한 선회 반경(Turning Radius)을 R이라고 하면 이상적인 관계는 tan(delta_inner)=L/(R-W/2), tan(delta_outer)=L/(R+W/2)로 표현할 수 있다. 이러한 방정식은 요구 곡률(Requested Curvature)과 실제 바퀴 조향각(Physical Wheel Steering Angle) 사이의 기본적인 기하학적 변환 관계를 구성한다.

편리한 소프트웨어 추상화(Software Abstraction) 방법으로 자전거 모델(Bicycle Model)을 사용할 수 있으며, 이 모델은 두 개의 전륜을 차축 중심에 위치한 하나의 등가 조향 바퀴(Equivalent Steering Wheel)로 대체한다. 등가 조향각(Equivalent Steering Angle)을 delta라고 하면 경로 곡률(Path Curvature)은 kappa=tan(delta)/L로 근사할 수 있고, 이에 대응하는 선회 반경은 R=L/tan(delta)로 표현된다. 이러한 단순화는 계산 복잡도(Computational Complexity)를 줄이며 특히 궤적 추종(Trajectory Tracking), 상태 추정(State Estimation), 시뮬레이션(Simulation), 상위 모션 제어 인터페이스(High-Level Motion-Control Interface)에 유용하다.

자전거 모델(Bicycle Model)은 차량의 위치 x와 y, 그리고 헤딩각(Heading Angle) psi를 이용하여 평면 차량 운동(Planar Vehicle Motion)을 기술한다. 저속 운동학 가정(Low-Speed Kinematic Assumption)에서 상태 방정식(State Equation)은 일반적으로 x_dot=v cos(psi), y_dot=v sin(psi), psi_dot=v tan(delta)/L로 표현된다. 이산 소프트웨어 구현(Discrete Software Implementation)은 정의된 제어 주기(Control Period)마다 이러한 방정식을 계산하여 실시간 조향 및 속도 명령과 일관되게 예측 자세(Predicted Pose)를 갱신할 수 있도록 한다.

소프트웨어 모델(Software Model)은 경로 곡률(Path Curvature), 등가 조향각(Equivalent Steering Angle), 로드 휠 각도(Road-Wheel Angle), 조향 액추에이터 위치(Steering Actuator Position), 조향 휠 또는 모터 측 각도(Steering-Wheel or Motor-Side Angle)를 명확하게 구분해야 한다. 이 변수들은 서로 관련되어 있지만 동일한 값으로 사용할 수 없다. 기계식 조향비(Mechanical Steering Ratio), 링크 기구 형상(Linkage Geometry), 액추에이터 감속비(Actuator Reduction Ratio), 오프셋(Offset), 비선형 특성(Nonlinear Characteristic)에 의해 한 표현이 다른 표현으로 변환될 수 있으므로 계획(Planning), 제어(Control), 액추에이터(Actuator), 피드백(Feedback) 영역 사이에 명시적인 변환 함수가 필요하다.

실제 조향 소프트웨어 파이프라인(Steering Software Pipeline)은 모션 제어 계층(Motion-Control Layer)으로부터 목표 곡률 또는 조향 명령을 입력받고 먼저 유효성 검사(Validity Check)와 범위 검사(Range Check)를 수행한다. 이후 명령을 등가 중심 조향각(Equivalent Center Steering Angle)으로 변환하고 다시 좌우 애커먼 바퀴 조향각으로 변환한다. 조향 한계(Steering Limit), 변화율 제한(Rate Limit), 가속도 제약(Acceleration Constraint), 액추에이터별 변환(Actuator-Specific Transformation)을 적용한 후 목표 명령을 조향 모터(Steering Motor) 또는 전자식 조향 제어기(Steering-by-Wire Controller)로 전달한다.

역변환(Inverse Transformation) 또한 중요하다. 측정된 조향 위치를 물리적으로 의미 있는 차량 상태(Vehicle State)로 다시 변환해야 하기 때문이다. 센서 측정값은 모터 엔코더 카운트(Motor Encoder Count), 액추에이터 변위(Actuator Displacement), 스티어링 랙 위치(Steering Rack Position), 개별 바퀴 조향각(Individual Wheel Angle) 등을 나타낼 수 있다. 보정 파라미터(Calibration Parameter)를 이용하여 이러한 측정값을 정규화된 조향 좌표(Normalized Steering Coordinate)로 변환한 후 소프트웨어는 피드백과 진단을 위한 등가 조향각, 곡률, 순간 선회 반경(Instantaneous Turning Radius)을 추정할 수 있다.

실제 조향 기구(Real Steering Mechanism)는 전체 동작 범위에서 이상적인 애커먼 기하학(Ideal Ackermann Geometry)을 완벽하게 만족하는 경우가 드물다. 타이로드(Tie Rod), 스티어링 암(Steering Arm), 서스펜션 움직임(Suspension Movement), 컴플라이언스(Compliance), 백래시(Backlash), 타이어 변형(Tire Deformation), 제조 공차(Manufacturing Tolerance)는 비선형적인 편차를 발생시킨다. 따라서 양산 소프트웨어(Production Software)는 이상적인 운동학 방정식을 기준 모델(Reference Model)로 유지하면서 룩업 테이블(Lookup Table), 다항식 보정(Polynomial Correction), 구간별 함수(Piecewise Function), 실험적으로 식별된 매핑(Experimentally Identified Mapping)을 추가할 수 있다.

조향각 포화(Steering Angle Saturation)는 일관된 방식으로 처리해야 한다. 과도하게 요구된 곡률은 물리적으로 구현할 수 없는 바퀴 명령을 생성할 수 있기 때문이다. 소프트웨어는 기계적 조향 한계(Mechanical Steering Limit)와 차량 기하학(Vehicle Geometry)을 기반으로 허용 가능한 곡률을 결정하고 역운동학 변환(Inverse Kinematic Conversion)을 수행하기 전에 명령을 제한해야 한다. 또한 구현 가능한 조향각이라 하더라도 차량이 움직이는 동안 조향 액추에이터가 해당 각도에 순간적으로 도달할 수 있다는 의미는 아니므로 변화율 제한(Rate Limiting)도 필요하다.

저속 애커먼 모델링(Low-Speed Ackermann Modeling)은 유의미한 횡방향 미끄러짐 없이 순수 구름(Pure Rolling)이 발생한다고 가정하며, 많은 실외 이동 로봇(Outdoor Mobile Robot), 자율 플랫폼(Autonomous Platform), 유틸리티 차량(Utility Vehicle), 저속 자율주행 시스템(Low-Speed Autonomous Driving System)에 적합하다. 속도가 증가하면 타이어 슬립각(Tire Slip Angle), 횡력(Lateral Force), 하중 이동(Load Transfer), 과도 동역학(Transient Dynamics)의 중요성이 증가한다. 따라서 소프트웨어 아키텍처(Software Architecture)는 운동학 모델이 유효한 운용 영역(Operating Region)을 정의하고 고정밀 동역학 모델(Higher-Fidelity Dynamic Model)을 위한 명확한 인터페이스를 제공해야 한다.

조향 모델(Steering Model)에서 사용하는 부호 규약(Sign Convention)은 모호하지 않도록 명확하게 정의해야 한다. 일반적인 구현에서는 전진 운동을 양의 종방향 속도(Positive Longitudinal Velocity), 좌회전을 양의 조향각(Positive Steering Angle), 반시계 방향 회전을 양의 요 레이트(Positive Yaw Rate)로 정의하지만 다른 규약도 사용할 수 있다. 좌표계(Coordinate Frame) 정의, 각도 단위(Angle Unit), 곡률 부호(Curvature Sign), 바퀴 식별자(Wheel Identifier)는 위치추정(Localization), 계획(Planning), 제어(Control), 시뮬레이션(Simulation), CAN 통신(CAN Communication), 진단(Diagnostics) 소프트웨어 전체에서 표준화해야 한다.

직진 주행 조건(Straight-Driving Condition) 부근에서는 조향각과 곡률이 0에 접근하고 계산된 선회 반경이 무한대에 접근하므로 수치적 강건성(Numerical Robustness)이 중요하다. 소프트웨어는 매우 작은 곡률 값으로 불필요하게 나누는 연산을 피하고 정의된 직선 주행 임계값(Straight-Line Threshold)을 사용해야 한다. 조향 한계 부근에서도 유사한 보호가 필요하며, 범위를 적절히 제한하지 않으면 탄젠트(Tangent)와 역탄젠트(Inverse Tangent) 계산이 수치 오차를 증폭시키거나 불연속성을 발생시킬 수 있다.

유지보수성이 높은 구현(Maintainable Implementation)은 차량 기하학(Vehicle Geometry)과 운동학 알고리즘(Kinematic Algorithm)을 분리한다. 휠베이스, 전후륜 윤거(Front and Rear Track Width), 최대 바퀴 조향각(Maximum Wheel Angle), 조향비(Steering Ratio), 영점 오프셋(Zero Offset), 보정 계수(Calibration Coefficient) 등의 파라미터는 구성 또는 보정 구조체(Configuration or Calibration Structure)에 저장해야 한다. 운동학 함수는 이 파라미터 집합을 기반으로 동작하도록 하여 핵심 조향 방정식을 변경하지 않고 동일한 소프트웨어 컴포넌트가 여러 로봇 변형 모델(Robot Variant)을 지원할 수 있도록 한다.

조향 모델은 유용한 진단 관계(Diagnostic Relationship)도 제공한다. 명령 조향각(Commanded Steering), 측정 바퀴 각도(Measured Wheel Angle), 차량 속도(Vehicle Velocity), 추정 요 레이트(Estimated Yaw Rate)를 이용하여 소프트웨어는 예상 곡률(Expected Curvature)과 관측 곡률(Observed Curvature)을 비교할 수 있다. 지속적인 불일치는 조향 센서 오프셋(Steering Sensor Offset), 액추에이터 추종 오차(Actuator Tracking Error), 기계식 링크 문제(Mechanical Linkage Problem), 타이어 미끄러짐(Tire Slip), 잘못된 보정(Incorrect Calibration)을 나타낼 수 있다. 잔차 임계값(Residual Threshold)과 시간 필터링(Temporal Filtering)을 이용하면 고장 로직을 액추에이터 제어에 직접 결합하지 않고도 이러한 모델 불일치를 진단 정보로 변환할 수 있다.

시험(Testing)은 직진 주행, 좌회전 및 우회전, 최소 선회 반경(Minimum-Radius Condition), 조향 포화, 후진 주행(Reverse Motion), 0에 가까운 수치 조건에 대한 결정론적 단위 시험(Deterministic Unit Test)부터 시작해야 한다. 대칭성 시험(Symmetry Test)을 통해 동일한 크기의 좌우 명령이 기하학적으로 대칭인 결과를 생성하는지 검증할 수 있다. 파라미터 경계 시험(Parameter-Boundary Test)은 잘못된 휠베이스, 윤거, 조향 한계 또는 유한하지 않은 명령값(Non-Finite Command Value)이 액추에이터 명령으로 전달되기 전에 탐지되는지 확인해야 한다.

소프트웨어 인 더 루프 시험(Software-in-the-Loop Testing)은 애커먼 모델을 궤적 생성기(Trajectory Generator) 및 경로 추종 제어기(Path-Tracking Controller)와 연결하여 곡률 연속성(Curvature Continuity)과 자세 변화(Pose Evolution)를 검증할 수 있다. 하드웨어 인 더 루프 시험(Hardware-in-the-Loop Testing)은 실제 조향 ECU, 모터 제어기, 센서, 통신 지연(Communication Delay), 액추에이터 동역학(Actuator Dynamics)까지 검증 범위를 확장한다. 기록된 명령 및 피드백 신호를 운동학 기준 모델과 비교하여 지연(Latency), 포화(Saturation), 히스테리시스(Hysteresis), 보정 오차(Calibration Error), 기계적 비대칭(Mechanical Asymmetry)을 식별할 수 있다.

통합 로봇 제어 아키텍처(Integrated Robot Control Architecture)에서 애커먼 운동학(Ackermann Kinematics)은 차량 수준의 운동 의도(Vehicle-Level Motion Intent)와 바퀴 수준의 조향 구동(Wheel-Level Steering Actuation) 사이를 연결하는 수학적 경계 역할을 한다. 플래너(Planner)는 주로 궤적(Trajectory)과 곡률을 기반으로 동작하고, 조향 제어기(Steering Controller)는 실제 바퀴 조향각과 액추에이터 명령을 관리할 수 있다. 이러한 분리는 소프트웨어 계층 사이의 의존성을 줄이고 플래너, 제어기, 조향 하드웨어, 차량 플랫폼이 서로 독립적으로 발전할 수 있도록 한다.

따라서 강건한 애커먼 조향 소프트웨어 모델(Robust Ackermann Steering Software Model)은 이상적인 기하학(Ideal Geometry), 명확한 좌표 규약(Coordinate Convention), 파라미터화된 차량 치수(Parameterized Vehicle Dimension), 액추에이터 매핑(Actuator Mapping), 수치 보호(Numerical Protection), 명령 제약(Command Constraint), 피드백 변환(Feedback Conversion), 모델 기반 진단(Model-Based Diagnostics)을 통합해야 한다. 이러한 책임을 명확하게 분리하면 동일한 운동학 기반을 시뮬레이션, 실시간 제어(Real-Time Control), 보정(Calibration), 시험, 현장 진단(Field Diagnostics)에 공통으로 사용할 수 있으며 이후의 경로 추종(Path Tracking) 및 조향 안전(Steering Safety) 기능에도 일관된 인터페이스를 제공할 수 있다.

##  

## 04.02 Four-Wheel Steering (4WS) Control Structure

![](images/image2.png){width="7.268055555555556in" height="7.268055555555556in"}

Four-wheel steering (4WS) extends conventional steering control by independently or coordinately controlling the steering angles of both the front and rear axles. Unlike standard Ackermann systems in which the rear wheels remain fixed, a 4WS platform introduces rear steering as an additional control degree of freedom. This enables the vehicle to modify turning radius, yaw response, lateral motion, and maneuverability according to operating speed and motion requirements.

The fundamental 4WS software model represents the vehicle using front steering angle, rear steering angle, wheelbase, track width, longitudinal velocity, yaw rate, and vehicle pose. The front and rear steering commands jointly determine the instantaneous center of rotation and resulting path curvature. Consequently, the control software must treat steering as a coordinated vehicle-level function rather than as two independent axle actuators receiving unrelated angle commands.

At low vehicle speeds, the front and rear wheels are commonly commanded in opposite directions, often called counter-phase steering. When the front wheels steer left, the rear wheels steer right, reducing the effective turning radius and allowing the vehicle to negotiate narrow passages or confined workspaces. This operating mode is particularly useful for outdoor AMRs, logistics platforms, inspection vehicles, and other robots requiring high maneuverability despite relatively long wheelbases.

At higher speeds, the front and rear wheels can steer in the same direction, known as in-phase steering. The rear steering angle is generally smaller than the front angle and is coordinated to improve lateral response and vehicle stability. Instead of primarily reducing turning radius, this mode reduces excessive yaw motion and can make lane changes or gradual trajectory corrections smoother by distributing lateral motion between both axles.

A simplified 4WS bicycle model replaces each axle with an equivalent center wheel having steering angles delta_f and delta_r. Under kinematic assumptions, vehicle curvature depends approximately on the difference between the front and rear steering directions. For small angles, curvature can be represented approximately by kappa=(delta_f-delta_r)/L, showing directly why opposite-phase steering increases curvature while same-phase steering reduces rotational response.

For larger steering angles, tangent-based relationships provide a more accurate representation than the small-angle approximation. The software model should therefore use geometrically consistent transformations when calculating curvature, turning radius, or axle steering commands. The exact equations depend on the selected vehicle reference point and coordinate convention, making explicit definitions of wheelbase, axle locations, steering signs, and pose reference essential for consistent implementation.

A 4WS control structure can be divided into vehicle motion command, steering coordination, axle command generation, actuator control, and feedback monitoring layers. The upper motion-control layer provides desired curvature, yaw behavior, or trajectory information. A steering coordinator then determines how the required motion should be distributed between the front and rear axles before individual steering controllers convert those targets into actuator commands.

The steering coordinator is the central software component distinguishing 4WS from conventional front-wheel steering. It determines the front-to-rear steering ratio according to vehicle speed, requested curvature, maneuvering mode, stability requirements, and mechanical limitations. A configurable distribution factor can continuously transition the system from counter-phase behavior at low speed through predominantly front steering and eventually toward in-phase behavior at higher speed.

Mode transition must be continuous because an abrupt change in rear steering direction can generate undesirable yaw or lateral acceleration. The control software should therefore use interpolation, rate limiting, hysteresis, or smooth scheduling functions across defined speed regions. Transition logic should also prevent repeated mode switching when vehicle speed oscillates near a threshold, while ensuring that commanded rear steering remains dynamically achievable.

A production 4WS system may support several operating modes beyond speed-dependent automatic steering. Conventional mode can hold the rear wheels near zero while using front steering only. Tight-turn mode can maximize counter-phase steering for minimum turning radius, while coordinated mode can continuously distribute steering between axles. Specialized robotic platforms may additionally support lateral or crab-like motion when their mechanical steering configuration permits approximately parallel wheel orientation.

Crab steering differs fundamentally from ordinary same-phase 4WS trajectory control because the objective is lateral translation with limited heading change rather than conventional turning. All steerable wheels may be aligned toward a common direction while the drive system generates motion along that orientation. Software should represent this as an explicit motion mode with dedicated kinematic constraints instead of treating it merely as an extreme value of the normal rear steering ratio.

The front and rear axle commands must ultimately be converted into physical left and right wheel angles. Each axle can apply Ackermann geometry using its axle-specific equivalent steering target, track width, and geometric relationship to the instantaneous center of rotation. This produces four wheel-angle targets that minimize geometric tire scrub while preserving the vehicle-level curvature requested by the steering coordinator.

Mechanical steering limits are especially important in 4WS because feasible combinations of front and rear angles form a constrained operating region. A requested curvature may be achievable mathematically but impossible because one axle reaches its angle, rate, torque, or actuator-position limit. The coordinator should therefore calculate feasible commands jointly and redistribute steering demand when possible instead of saturating each axle independently.

Feedback processing should maintain separate measurements for front and rear steering positions while also estimating a combined vehicle steering state. Encoder counts, steering-angle sensors, rack positions, or actuator positions are converted through calibration models into physical wheel or equivalent axle angles. The resulting values can be used to estimate expected curvature and yaw rate and compared with localization, IMU, or vehicle-motion measurements.

Synchronization between the front and rear steering actuators is critical during rapid command changes. Different actuator bandwidths, communication delays, mechanical loads, or control-loop periods can temporarily produce an unintended steering geometry. The software can compensate using synchronized command timestamps, matched trajectory profiles, feedforward terms, or delay compensation so that both axles follow coordinated steering trajectories rather than simply reaching identical completion times.

The actuator-control layer normally contains position, velocity, or torque loops for each steering mechanism. These low-level loops should remain separated from vehicle-level 4WS coordination so that actuator hardware can be replaced without redesigning the kinematic controller. The coordinator generates physically meaningful axle targets, while hardware-specific controllers manage motor current, gear reduction, friction, backlash, steering torque, and local position tracking.

Safety supervision must consider both common and axle-specific failures. A front or rear steering sensor disagreement, actuator tracking error, communication timeout, excessive steering rate, or unexpected wheel position can invalidate the coordinated geometry. Depending on platform design and fault severity, the system may transition to front-steering-only operation, center the rear axle, limit vehicle speed, enter a degraded control mode, or command a controlled safe stop.

Rear steering return-to-center behavior deserves explicit treatment because an uncontrolled rear angle can significantly alter vehicle motion even when front steering appears normal. During startup, shutdown, fault recovery, emergency transitions, or mode changes, software should verify the rear steering position before allowing unrestricted motion. Position plausibility and actuator availability should therefore be part of the steering system readiness conditions.

Diagnostics can compare commanded and measured angles for all steerable wheels and evaluate whether the combined geometry produces the expected vehicle response. Residuals between predicted curvature, measured yaw rate, and estimated trajectory can reveal calibration drift, mechanical misalignment, actuator degradation, tire slip, or synchronization errors. Diagnostic thresholds should account for speed and operating mode because expected residual behavior differs between tight turns and normal travel.

Parameter management is essential because 4WS behavior depends strongly on vehicle geometry and steering hardware. Wheelbase, front and rear track widths, maximum steering angles, actuator rates, steering ratios, calibration offsets, speed thresholds, transition gains, and rear-steering distribution maps should be maintained as traceable calibration data. Separating these parameters from algorithm code allows one control architecture to support different vehicle dimensions and actuator configurations.

Verification should include straight motion, symmetric left and right turns, counter-phase steering, in-phase steering, transition regions, rear-steering saturation, actuator delays, reverse driving, and degraded modes. Software-in-the-loop testing can validate kinematic consistency and trajectory response, while hardware-in-the-loop testing can introduce sensor faults, communication delays, actuator saturation, and mismatched front-rear dynamics before field testing.

Within the overall robot control architecture, the 4WS module forms a coordinated interface between trajectory-level motion requirements and multiple steering actuators. The planner does not need to manage individual wheel angles directly; instead, it provides vehicle-level motion intent. The 4WS controller interprets that intent according to geometry, speed, operating mode, constraints, and safety state before distributing commands to the physical steering system.

A well-structured 4WS control system therefore combines vehicle kinematics, front-rear steering coordination, smooth mode scheduling, Ackermann wheel conversion, actuator synchronization, feedback estimation, constraint management, diagnostics, and fault handling. By separating these responsibilities into clear software layers, four-wheel steering can provide improved maneuverability and controllability while remaining testable, calibratable, and reusable across different autonomous mobile robot and robotic vehicle platforms.

4륜 조향(Four-Wheel Steering, 4WS)은 전륜축(Front Axle)과 후륜축(Rear Axle)의 조향각을 독립적으로 또는 상호 연계하여 제어함으로써 기존 조향 제어(Conventional Steering Control)를 확장한다. 후륜이 고정되는 일반적인 애커먼 시스템(Ackermann System)과 달리 4WS 플랫폼은 후륜 조향(Rear Steering)을 추가적인 제어 자유도(Control Degree of Freedom)로 도입한다. 이를 통해 차량은 운행 속도와 운동 요구사항에 따라 선회 반경(Turning Radius), 요 응답(Yaw Response), 횡방향 운동(Lateral Motion), 기동성(Maneuverability)을 조절할 수 있다.

기본적인 4WS 소프트웨어 모델(Software Model)은 전륜 조향각(Front Steering Angle), 후륜 조향각(Rear Steering Angle), 휠베이스(Wheelbase), 윤거(Track Width), 종방향 속도(Longitudinal Velocity), 요 레이트(Yaw Rate), 차량 자세(Vehicle Pose)를 이용하여 차량을 표현한다. 전륜과 후륜의 조향 명령은 순간 회전 중심(Instantaneous Center of Rotation)과 이에 따른 경로 곡률(Path Curvature)을 공동으로 결정한다. 따라서 제어 소프트웨어는 조향을 서로 관련 없는 각도 명령을 받는 두 개의 독립적인 차축 액추에이터가 아니라 통합된 차량 수준 기능(Vehicle-Level Function)으로 처리해야 한다.

저속 차량 운행에서는 일반적으로 전륜과 후륜을 서로 반대 방향으로 명령하며, 이를 역위상 조향(Counter-Phase Steering)이라고 한다. 전륜이 왼쪽으로 조향될 때 후륜은 오른쪽으로 조향되어 유효 선회 반경(Effective Turning Radius)을 감소시키고 차량이 좁은 통로나 제한된 작업 공간을 통과할 수 있도록 한다. 이러한 운전 모드는 비교적 긴 휠베이스를 가지면서 높은 기동성이 필요한 실외 자율이동로봇(Outdoor AMR), 물류 플랫폼(Logistics Platform), 검사 차량(Inspection Vehicle) 등에 특히 유용하다.

고속에서는 전륜과 후륜을 동일한 방향으로 조향할 수 있으며, 이를 동위상 조향(In-Phase Steering)이라고 한다. 일반적으로 후륜 조향각은 전륜 조향각보다 작으며 횡방향 응답(Lateral Response)과 차량 안정성(Vehicle Stability)을 향상하도록 상호 조정된다. 이 모드는 주로 선회 반경을 줄이기보다는 과도한 요 운동(Yaw Motion)을 감소시키고 양쪽 차축에 횡방향 운동을 분산시켜 차선 변경(Lane Change)이나 완만한 궤적 보정(Trajectory Correction)을 더욱 부드럽게 수행하도록 한다.

단순화된 4WS 자전거 모델(Bicycle Model)은 각각의 차축을 조향각 delta_f와 delta_r을 갖는 하나의 등가 중앙 바퀴(Equivalent Center Wheel)로 대체한다. 운동학적 가정(Kinematic Assumption)에서 차량 곡률(Vehicle Curvature)은 전륜과 후륜의 조향 방향 차이에 의해 근사적으로 결정된다. 작은 조향각에서는 곡률을 kappa=(delta_f-delta_r)/L로 근사할 수 있으며, 이 관계를 통해 역위상 조향이 곡률을 증가시키고 동위상 조향이 회전 응답(Rotational Response)을 감소시키는 이유를 직접 확인할 수 있다.

큰 조향각에서는 소각도 근사(Small-Angle Approximation)보다 탄젠트 기반 관계(Tangent-Based Relationship)가 더욱 정확한 표현을 제공한다. 따라서 소프트웨어 모델은 곡률, 선회 반경 또는 차축 조향 명령을 계산할 때 기하학적으로 일관된 변환(Geometrically Consistent Transformation)을 사용해야 한다. 정확한 방정식은 선택한 차량 기준점(Vehicle Reference Point)과 좌표 규약(Coordinate Convention)에 따라 달라지므로 휠베이스, 차축 위치, 조향 부호(Steering Sign), 자세 기준(Pose Reference)을 명확하게 정의하는 것이 일관된 구현을 위해 필수적이다.

4WS 제어 구조(Control Structure)는 차량 운동 명령(Vehicle Motion Command), 조향 협조 제어(Steering Coordination), 차축 명령 생성(Axle Command Generation), 액추에이터 제어(Actuator Control), 피드백 모니터링(Feedback Monitoring) 계층으로 구분할 수 있다. 상위 모션 제어 계층(Motion-Control Layer)은 목표 곡률, 요 거동(Yaw Behavior), 궤적 정보를 제공한다. 이후 조향 협조 제어기(Steering Coordinator)는 필요한 운동을 전륜축과 후륜축 사이에 어떻게 분배할지 결정한 다음 개별 조향 제어기가 해당 목표값을 액추에이터 명령으로 변환한다.

조향 협조 제어기(Steering Coordinator)는 4WS를 기존 전륜 조향(Front-Wheel Steering)과 구분하는 핵심 소프트웨어 컴포넌트이다. 이 제어기는 차량 속도, 요구 곡률(Requested Curvature), 기동 모드(Maneuvering Mode), 안정성 요구사항(Stability Requirement), 기계적 한계(Mechanical Limitation)에 따라 전륜과 후륜 사이의 조향 비율을 결정한다. 설정 가능한 분배 계수(Distribution Factor)를 사용하면 저속의 역위상 동작에서 주로 전륜을 사용하는 영역을 거쳐 고속의 동위상 동작으로 시스템을 연속적으로 전환할 수 있다.

후륜 조향 방향이 갑자기 변경되면 바람직하지 않은 요 또는 횡가속도(Lateral Acceleration)가 발생할 수 있으므로 모드 전환(Mode Transition)은 연속적으로 이루어져야 한다. 따라서 제어 소프트웨어는 정의된 속도 영역에 걸쳐 보간(Interpolation), 변화율 제한(Rate Limiting), 히스테리시스(Hysteresis), 부드러운 스케줄링 함수(Smooth Scheduling Function)를 사용해야 한다. 전환 로직(Transition Logic)은 차량 속도가 임계값 부근에서 변동할 때 반복적인 모드 전환을 방지하면서 명령된 후륜 조향이 동역학적으로 구현 가능한 상태를 유지해야 한다.

양산형 4WS 시스템(Production 4WS System)은 속도 기반 자동 조향(Speed-Dependent Automatic Steering) 이외에도 여러 운전 모드를 지원할 수 있다. 일반 모드(Conventional Mode)는 후륜을 거의 0도에 유지하면서 전륜 조향만 사용할 수 있다. 최소 회전 모드(Tight-Turn Mode)는 최소 선회 반경을 위해 역위상 조향을 최대화하고, 협조 모드(Coordinated Mode)는 두 차축 사이에 조향을 연속적으로 분배한다. 특수 로봇 플랫폼은 기계적 조향 구조가 거의 평행한 바퀴 방향을 허용하는 경우 횡방향 또는 크랩 형태의 운동(Crab-Like Motion)을 추가로 지원할 수 있다.

크랩 조향(Crab Steering)은 목표가 일반적인 선회가 아니라 헤딩 변화(Heading Change)를 최소화하면서 횡방향으로 이동하는 것이므로 일반적인 동위상 4WS 궤적 제어와 근본적으로 다르다. 모든 조향 가능한 바퀴를 공통 방향으로 정렬하고 구동 시스템(Drive System)이 해당 방향을 따라 차량을 이동시킬 수 있다. 소프트웨어는 이를 단순히 일반적인 후륜 조향 비율의 극단적인 값으로 처리하지 않고 전용 운동학 제약(Kinematic Constraint)을 갖는 명시적인 운동 모드(Motion Mode)로 표현해야 한다.

전륜축과 후륜축 명령은 최종적으로 실제 좌우 바퀴 조향각(Physical Left and Right Wheel Angle)으로 변환되어야 한다. 각 차축은 해당 차축의 등가 조향 목표(Equivalent Steering Target), 윤거, 순간 회전 중심과의 기하학적 관계를 이용하여 애커먼 기하학(Ackermann Geometry)을 적용할 수 있다. 이를 통해 조향 협조 제어기가 요구한 차량 수준의 곡률을 유지하면서 기하학적 타이어 스크럽(Tire Scrub)을 최소화하는 네 개의 바퀴 조향각 목표를 생성할 수 있다.

4WS에서는 전륜과 후륜 조향각의 구현 가능한 조합이 제한된 운용 영역(Constrained Operating Region)을 형성하기 때문에 기계적 조향 한계(Mechanical Steering Limit)가 특히 중요하다. 요구 곡률이 수학적으로 가능하더라도 하나의 차축이 조향각, 변화율, 토크 또는 액추에이터 위치 한계에 도달하면 실제로 구현할 수 없을 수 있다. 따라서 협조 제어기는 각 차축을 독립적으로 포화(Saturation)시키기보다 가능한 명령을 통합적으로 계산하고 필요하면 조향 요구량을 재분배해야 한다.

피드백 처리(Feedback Processing)는 전륜과 후륜의 조향 위치를 개별적으로 측정하면서 통합 차량 조향 상태(Combined Vehicle Steering State)도 추정해야 한다. 엔코더 카운트(Encoder Count), 조향각 센서(Steering-Angle Sensor), 랙 위치(Rack Position), 액추에이터 위치는 보정 모델(Calibration Model)을 통해 실제 바퀴 조향각 또는 등가 차축 조향각으로 변환된다. 이 값을 이용하여 예상 곡률(Expected Curvature)과 요 레이트를 추정하고 위치추정(Localization), 관성측정장치(IMU), 차량 운동 측정값(Vehicle-Motion Measurement)과 비교할 수 있다.

급격한 명령 변화 중에는 전륜과 후륜 조향 액추에이터 사이의 동기화(Synchronization)가 중요하다. 서로 다른 액추에이터 대역폭(Actuator Bandwidth), 통신 지연(Communication Delay), 기계적 부하(Mechanical Load), 제어 루프 주기(Control-Loop Period)는 일시적으로 의도하지 않은 조향 기하학을 발생시킬 수 있다. 소프트웨어는 동기화된 명령 타임스탬프(Command Timestamp), 일치된 궤적 프로파일(Matched Trajectory Profile), 피드포워드 항(Feedforward Term), 지연 보상(Delay Compensation)을 사용하여 두 차축이 단순히 동일한 시점에 목표 위치에 도달하는 것이 아니라 상호 조정된 조향 궤적을 추종하도록 할 수 있다.

액추에이터 제어 계층(Actuator-Control Layer)은 일반적으로 각 조향 장치에 대한 위치(Position), 속도(Velocity), 토크(Torque) 제어 루프를 포함한다. 이러한 하위 제어 루프(Low-Level Control Loop)는 차량 수준의 4WS 협조 제어와 분리되어야 하며, 이를 통해 운동학 제어기를 재설계하지 않고도 액추에이터 하드웨어를 교체할 수 있다. 협조 제어기는 물리적으로 의미 있는 차축 목표를 생성하고 하드웨어별 제어기(Hardware-Specific Controller)는 모터 전류, 감속비(Gear Reduction), 마찰(Friction), 백래시(Backlash), 조향 토크, 로컬 위치 추종(Local Position Tracking)을 관리한다.

안전 감독(Safety Supervision)은 공통 고장(Common Failure)과 차축별 고장(Axle-Specific Failure)을 모두 고려해야 한다. 전륜 또는 후륜 조향 센서 불일치, 액추에이터 추종 오차(Actuator Tracking Error), 통신 타임아웃(Communication Timeout), 과도한 조향 변화율 또는 예상하지 못한 바퀴 위치는 협조 조향 기하학을 무효화할 수 있다. 플랫폼 설계와 고장 심각도에 따라 시스템은 전륜 조향 전용 운전(Front-Steering-Only Operation), 후륜 중앙 복귀, 차량 속도 제한, 성능 저하 제어 모드(Degraded Control Mode), 제어된 안전 정지(Controlled Safe Stop)로 전환할 수 있다.

제어되지 않은 후륜 조향각은 전륜 조향이 정상적으로 보이더라도 차량 운동을 크게 변화시킬 수 있으므로 후륜 중앙 복귀 동작(Rear Steering Return-to-Center Behavior)을 명시적으로 처리해야 한다. 시동(Startup), 종료(Shutdown), 고장 복구(Fault Recovery), 비상 전환(Emergency Transition), 모드 변경 과정에서 소프트웨어는 제한 없는 차량 운동을 허용하기 전에 후륜 조향 위치를 확인해야 한다. 따라서 위치 타당성(Position Plausibility)과 액추에이터 가용성(Actuator Availability)은 조향 시스템 준비 조건(Steering System Readiness Condition)에 포함되어야 한다.

진단(Diagnostics)은 모든 조향 가능 바퀴에 대해 명령각과 측정각을 비교하고 통합된 기하학이 예상한 차량 응답을 생성하는지 평가할 수 있다. 예측 곡률(Predicted Curvature), 측정 요 레이트(Measured Yaw Rate), 추정 궤적(Estimated Trajectory) 사이의 잔차(Residual)는 보정 드리프트(Calibration Drift), 기계적 정렬 오차(Mechanical Misalignment), 액추에이터 성능 저하(Actuator Degradation), 타이어 미끄러짐(Tire Slip), 동기화 오차(Synchronization Error)를 검출하는 데 활용할 수 있다. 예상되는 잔차 특성은 최소 반경 선회와 일반 주행에서 서로 다르므로 진단 임계값은 속도와 운전 모드를 고려해야 한다.

4WS 동작은 차량 기하학과 조향 하드웨어에 크게 의존하므로 파라미터 관리(Parameter Management)가 필수적이다. 휠베이스, 전륜 및 후륜 윤거, 최대 조향각(Maximum Steering Angle), 액추에이터 변화율(Actuator Rate), 조향비(Steering Ratio), 보정 오프셋(Calibration Offset), 속도 임계값(Speed Threshold), 전환 게인(Transition Gain), 후륜 조향 분배 맵(Rear-Steering Distribution Map)은 추적 가능한 보정 데이터(Traceable Calibration Data)로 관리해야 한다. 이러한 파라미터를 알고리즘 코드와 분리하면 하나의 제어 아키텍처로 서로 다른 차량 치수와 액추에이터 구성을 지원할 수 있다.

검증(Verification)은 직진 운동, 대칭적인 좌우 선회, 역위상 조향, 동위상 조향, 전환 영역, 후륜 조향 포화, 액추에이터 지연, 후진 주행(Reverse Driving), 성능 저하 모드(Degraded Mode)를 포함해야 한다. 소프트웨어 인 더 루프 시험(Software-in-the-Loop Testing)은 운동학적 일관성과 궤적 응답을 검증할 수 있으며, 하드웨어 인 더 루프 시험(Hardware-in-the-Loop Testing)은 현장 시험(Field Testing)에 앞서 센서 고장, 통신 지연, 액추에이터 포화, 전후륜 동역학 불일치(Mismatched Front-Rear Dynamics)를 주입하여 검증할 수 있다.

전체 로봇 제어 아키텍처(Robot Control Architecture)에서 4WS 모듈은 궤적 수준의 운동 요구사항(Trajectory-Level Motion Requirement)과 다수의 조향 액추에이터 사이를 연결하는 협조 인터페이스(Coordinated Interface)를 형성한다. 플래너(Planner)는 개별 바퀴 조향각을 직접 관리할 필요 없이 차량 수준의 운동 의도(Vehicle-Level Motion Intent)를 제공한다. 4WS 제어기는 차량 기하학, 속도, 운전 모드, 제약 조건, 안전 상태(Safety State)를 기반으로 이러한 의도를 해석한 후 실제 조향 시스템에 명령을 분배한다.

잘 구조화된 4WS 제어 시스템(Well-Structured 4WS Control System)은 차량 운동학(Vehicle Kinematics), 전후륜 조향 협조(Front-Rear Steering Coordination), 부드러운 모드 스케줄링(Smooth Mode Scheduling), 애커먼 바퀴 변환(Ackermann Wheel Conversion), 액추에이터 동기화, 피드백 추정(Feedback Estimation), 제약 조건 관리(Constraint Management), 진단, 고장 처리를 통합한다. 이러한 기능을 명확한 소프트웨어 계층으로 분리함으로써 4륜 조향은 향상된 기동성과 제어 성능을 제공하면서도 서로 다른 자율이동로봇 및 로봇 차량 플랫폼에서 시험 가능하고, 보정 가능하며, 재사용 가능한 구조를 유지할 수 있다.

##  

## 04.03 Steer-By-Wire (SbW) Software Design

![](images/image3.png){width="7.268055555555556in" height="7.268055555555556in"}

Steer-by-Wire (SbW) replaces or supplements the mechanical steering connection between the steering command source and road wheels with electronic sensing, computation, communication, and electric actuation. In autonomous robots, the command source may be a trajectory controller rather than a human steering wheel. The SbW software therefore becomes the functional link that converts requested vehicle motion into controlled steering actuator movement.

A typical SbW architecture contains a command interface, steering supervisor, target-angle generator, actuator controller, sensor-processing layer, safety monitor, diagnostics manager, and communication interface. These functions may execute within one steering ECU or be distributed across redundant controllers. The software architecture should maintain clear boundaries between vehicle-level steering decisions, actuator-level closed-loop control, and independent safety supervision.

The command interface receives steering requests expressed as curvature, equivalent steering angle, wheel angle, steering rate, or actuator position. Autonomous platforms commonly receive curvature or trajectory-related commands from the motion-control layer. The SbW software validates command range, timestamp, source identity, operating mode, and freshness before accepting the request, preventing stale or malformed commands from directly influencing steering actuators.

Command arbitration becomes necessary when several sources can request steering simultaneously. Autonomous control, remote operation, manual service control, safety functions, and emergency logic may each produce steering requests. A deterministic priority mechanism should define which source has authority under each operating state. Authority transitions must be controlled so that switching between command sources does not introduce discontinuous steering-angle or steering-rate commands.

The target-generation layer converts the accepted vehicle-level request into a physical steering target. For Ackermann vehicles, requested curvature may first be converted into an equivalent steering angle and then into individual road-wheel targets. Four-wheel-steering platforms additionally require coordinated front and rear targets. Mechanical steering ratio, linkage geometry, actuator travel, calibration offsets, and nonlinear mappings are applied before generating the final actuator reference.

Target commands should pass through steering-angle, steering-rate, and steering-acceleration constraints before reaching the low-level controller. These limits protect mechanical components and prevent abrupt lateral vehicle motion. Limits can depend on vehicle speed, operating mode, payload, actuator temperature, or safety state. Dynamic constraints are especially important because an angle that is mechanically achievable at standstill may be unsafe or dynamically inappropriate at higher speed.

The actuator controller forms the real-time closed loop responsible for tracking the steering target. Depending on hardware architecture, the inner loops may regulate motor current or torque, while outer loops regulate actuator velocity and position. Feedforward compensation can improve response, while friction, backlash, and hysteresis compensation can reduce steady tracking errors. Controller gains may also be scheduled according to operating conditions and steering load.

Sensor processing converts raw measurements into trustworthy steering-state information. Typical signals include motor encoder position, steering-angle sensor output, rack displacement, motor current, torque, temperature, supply voltage, and actuator velocity. Filtering should suppress noise without introducing excessive phase delay, while calibration converts electrical or encoder measurements into physically meaningful angles and positions used by control and diagnostic functions.

Redundant sensing is commonly required because steering position is safety critical. Two independent angle measurements may be compared continuously using range, rate, correlation, and plausibility checks. Disagreement beyond calibrated limits should generate a fault rather than silently selecting an arbitrary signal. Where analytical redundancy is available, motor position, rack position, wheel angle, and vehicle yaw response can provide additional evidence about steering-system integrity.

Communication design is another critical part of SbW software because commands and feedback may cross CAN, CAN FD, Automotive Ethernet, EtherCAT, or other deterministic networks. Each message should contain sufficient information for freshness and integrity checking, such as counters, timestamps, state information, and error-detection fields where supported. Timeout monitoring ensures that loss of communication is recognized within a defined fault-tolerant time interval.

The steering state machine coordinates initialization, standby, enabled operation, degraded operation, fault handling, calibration, and shutdown. Steering torque or position control should not become active merely because software execution has started. Sensor plausibility, communication availability, actuator readiness, power condition, calibration validity, and safety authorization should be confirmed before transitioning into an operational state capable of producing vehicle motion.

Startup behavior requires particular attention because steering position may be unknown or offset after power cycling. The system may use absolute position sensing, stored calibration information, mechanical references, or controlled homing depending on hardware design. The software should avoid uncontrolled steering movement during initialization and should verify that the interpreted steering position is consistent with redundant sensors before normal command tracking is enabled.

Fault handling should distinguish between transient, recoverable, degraded, and critical failures. Examples include sensor disagreement, motor overcurrent, actuator overtemperature, communication timeout, excessive tracking error, invalid calibration, unexpected steering motion, or controller watchdog expiration. Each fault should map to a defined response such as command limitation, reduced steering rate, redundant-channel takeover, controlled centering, degraded operation, or safe vehicle stop.

A safety monitor should operate with sufficient independence from the nominal steering controller to detect unreasonable behavior. It can supervise commanded versus measured angle, maximum steering rate, actuator current, execution timing, communication status, and control-state transitions. Independent monitoring reduces the possibility that a single software defect simultaneously generates an incorrect command and incorrectly declares the resulting steering behavior valid.

Redundant SbW architectures may contain dual processing channels, duplicated sensors, separate power paths, or multiple steering actuators. Software must coordinate channel health, cross-check important states, and define deterministic rules for failover. Redundancy is useful only when common-cause failures and disagreement handling are considered; simply duplicating identical calculations without independent supervision does not automatically provide fault-tolerant steering.

Timing determinism directly affects steering stability and safety. Sensor acquisition, control computation, communication, actuator updates, and diagnostic supervision should execute with bounded periods and latency. Timestamped data helps distinguish physical steering errors from delayed measurements. Real-time scheduling should assign appropriate priorities so that logging, diagnostics, or noncritical communication cannot delay the steering control loop beyond its permitted execution window.

Steering feedback should also be supplied to higher-level vehicle software. Measured wheel angle, equivalent steering angle, actuator status, steering rate, fault state, and validity information can be consumed by localization, motion control, trajectory tracking, and safety functions. Publishing both the value and its quality state prevents upper layers from assuming that a numerically available steering measurement is necessarily trustworthy.

Calibration management connects the generic SbW software to the actual steering mechanism. Zero offsets, steering ratios, wheel-angle limits, actuator travel, sensor scaling, nonlinear linkage maps, controller gains, diagnostic thresholds, and rate constraints should be stored as version-controlled parameters. Software should verify parameter integrity at startup and maintain traceability between calibration versions, software releases, and vehicle configurations.

Diagnostics and logging should capture enough context to reconstruct abnormal steering events. Command source, requested angle, limited target, measured positions, actuator current, controller state, fault flags, timestamps, communication status, and safety transitions are particularly useful. High-rate signals may be stored in a circular buffer so that data immediately before and after a fault can be preserved without continuously recording excessive amounts of information.

Software verification should begin with unit tests covering conversions, limits, state transitions, sensor plausibility, command arbitration, timeout handling, and fault responses. Software-in-the-loop testing can evaluate closed-loop steering behavior with simulated vehicle and actuator models. Hardware-in-the-loop testing can introduce communication loss, sensor offsets, actuator delays, current limits, processor timing faults, and redundant-channel disagreement under repeatable conditions.

Vehicle-level validation should confirm not only actuator tracking accuracy but also the resulting motion behavior. Tests should include straight-line holding, gradual turns, maximum steering commands, rapid reversals, low- and high-speed operation, command-source transitions, startup and shutdown, degraded modes, and emergency responses. Measured yaw rate and trajectory curvature can be compared with values predicted from the steering kinematic model to detect system-level inconsistencies.

Cybersecurity should be considered because electronic steering commands create an attack surface that does not exist in purely mechanical steering links. Command interfaces should restrict unauthorized sources, communication should provide appropriate authenticity and integrity protection, and diagnostic or service functions should not bypass steering authority controls. Security mechanisms must be designed so that they preserve deterministic safety behavior during communication or authentication failures.

Within the robot control stack, SbW should present a stable interface between motion-control algorithms and hardware-specific steering mechanisms. Path-tracking software can request vehicle-level steering behavior without knowing motor encoder counts or rack geometry, while the SbW layer handles conversion, closed-loop actuation, monitoring, and fault management. This separation enables steering hardware and higher-level autonomy software to evolve with reduced mutual dependency.

A robust SbW software design therefore integrates command validation, authority management, steering kinematics, actuator control, redundant sensing, deterministic communication, state management, safety monitoring, diagnostics, calibration, and verification. Treating these elements as coordinated layers rather than isolated functions allows electronic steering to provide precise controllability while maintaining predictable behavior during normal operation, degraded conditions, and system faults.

전자식 조향(Steer-by-Wire, SbW)은 조향 명령원(Steering Command Source)과 실제 바퀴(Road Wheels) 사이의 기계적 조향 연결(Mechanical Steering Connection)을 전자 센싱(Electronic Sensing), 연산(Computation), 통신(Communication), 전기식 구동(Electric Actuation)으로 대체하거나 보완한다. 자율 로봇에서는 사람의 스티어링 휠 대신 궤적 제어기(Trajectory Controller)가 명령원이 될 수 있다. 따라서 SbW 소프트웨어는 요구된 차량 운동을 제어된 조향 액추에이터 움직임으로 변환하는 기능적 연결부(Functional Link)가 된다.

일반적인 SbW 아키텍처(Architecture)는 명령 인터페이스(Command Interface), 조향 감독기(Steering Supervisor), 목표각 생성기(Target-Angle Generator), 액추에이터 제어기(Actuator Controller), 센서 처리 계층(Sensor-Processing Layer), 안전 모니터(Safety Monitor), 진단 관리자(Diagnostics Manager), 통신 인터페이스(Communication Interface)로 구성된다. 이러한 기능은 하나의 조향 ECU에서 실행되거나 이중화된 제어기(Redundant Controller)에 분산될 수 있다. 소프트웨어 아키텍처는 차량 수준 조향 결정, 액추에이터 수준 폐루프 제어(Closed-Loop Control), 독립적인 안전 감독 사이에 명확한 경계를 유지해야 한다.

명령 인터페이스는 곡률(Curvature), 등가 조향각(Equivalent Steering Angle), 바퀴 조향각(Wheel Angle), 조향 변화율(Steering Rate), 액추에이터 위치(Actuator Position) 등으로 표현된 조향 요구를 수신한다. 자율 플랫폼은 일반적으로 모션 제어 계층(Motion-Control Layer)으로부터 곡률 또는 궤적 관련 명령을 수신한다. SbW 소프트웨어는 명령을 수락하기 전에 명령 범위, 타임스탬프(Timestamp), 명령원 식별 정보(Source Identity), 운전 모드(Operating Mode), 최신성(Freshness)을 검증하여 오래되거나 잘못 구성된 명령이 조향 액추에이터에 직접 영향을 주지 않도록 한다.

여러 명령원이 동시에 조향을 요구할 수 있는 경우 명령 중재(Command Arbitration)가 필요하다. 자율 제어(Autonomous Control), 원격 운전(Remote Operation), 수동 서비스 제어(Manual Service Control), 안전 기능(Safety Function), 비상 로직(Emergency Logic)은 각각 조향 명령을 생성할 수 있다. 결정론적 우선순위 메커니즘(Deterministic Priority Mechanism)은 각 운전 상태에서 어느 명령원이 제어 권한을 갖는지 정의해야 한다. 명령 권한 전환(Authority Transition)은 명령원 전환 과정에서 불연속적인 조향각 또는 조향 변화율 명령이 발생하지 않도록 제어되어야 한다.

목표 생성 계층(Target-Generation Layer)은 수락된 차량 수준 요구를 실제 조향 목표(Physical Steering Target)로 변환한다. 애커먼 차량(Ackermann Vehicle)에서는 요구 곡률을 먼저 등가 조향각으로 변환한 후 개별 바퀴 목표각으로 변환할 수 있다. 4륜 조향 플랫폼(Four-Wheel-Steering Platform)은 추가적으로 상호 조정된 전륜 및 후륜 목표를 필요로 한다. 최종 액추에이터 기준값(Actuator Reference)을 생성하기 전에 기계적 조향비(Mechanical Steering Ratio), 링크 기구 형상(Linkage Geometry), 액추에이터 이동 거리(Actuator Travel), 보정 오프셋(Calibration Offset), 비선형 매핑(Nonlinear Mapping)을 적용한다.

목표 명령(Target Command)은 하위 제어기(Low-Level Controller)에 전달되기 전에 조향각, 조향 변화율, 조향 가속도(Steering Acceleration) 제약을 통과해야 한다. 이러한 제한은 기계 부품을 보호하고 급격한 차량 횡방향 운동(Lateral Vehicle Motion)을 방지한다. 제한값은 차량 속도, 운전 모드, 페이로드(Payload), 액추에이터 온도, 안전 상태(Safety State)에 따라 달라질 수 있다. 정지 상태에서 기계적으로 구현 가능한 조향각도 고속에서는 안전하지 않거나 동역학적으로 적절하지 않을 수 있으므로 동적 제약(Dynamic Constraint)이 특히 중요하다.

액추에이터 제어기(Actuator Controller)는 조향 목표를 추종하는 실시간 폐루프(Real-Time Closed Loop)를 구성한다. 하드웨어 아키텍처에 따라 내부 루프(Inner Loop)는 모터 전류 또는 토크를 제어하고 외부 루프(Outer Loop)는 액추에이터 속도와 위치를 제어할 수 있다. 피드포워드 보상(Feedforward Compensation)은 응답 성능을 향상시키며, 마찰(Friction), 백래시(Backlash), 히스테리시스(Hysteresis) 보상은 정상상태 추종 오차(Steady Tracking Error)를 감소시킬 수 있다. 제어기 게인(Controller Gain) 역시 운전 조건과 조향 부하에 따라 스케줄링할 수 있다.

센서 처리(Sensor Processing)는 원시 측정값(Raw Measurement)을 신뢰할 수 있는 조향 상태 정보(Steering-State Information)로 변환한다. 대표적인 신호에는 모터 엔코더 위치(Motor Encoder Position), 조향각 센서 출력(Steering-Angle Sensor Output), 랙 변위(Rack Displacement), 모터 전류, 토크, 온도, 공급 전압(Supply Voltage), 액추에이터 속도가 포함된다. 필터링(Filtering)은 과도한 위상 지연(Phase Delay)을 발생시키지 않으면서 노이즈를 억제해야 하며, 보정(Calibration)은 전기 신호 또는 엔코더 측정값을 제어 및 진단 기능에서 사용하는 물리적으로 의미 있는 각도와 위치로 변환한다.

조향 위치는 안전에 중요한 요소이므로 일반적으로 이중화 센싱(Redundant Sensing)이 요구된다. 두 개의 독립적인 각도 측정값은 범위(Range), 변화율(Rate), 상관관계(Correlation), 타당성(Plausibility) 검사를 통해 지속적으로 비교할 수 있다. 보정된 한계를 초과하는 불일치는 임의의 신호를 자동 선택하기보다 고장(Fault)으로 처리해야 한다. 해석적 이중화(Analytical Redundancy)가 가능한 경우 모터 위치, 랙 위치, 바퀴 조향각, 차량 요 응답(Vehicle Yaw Response)을 이용하여 조향 시스템의 건전성(Steering-System Integrity)을 추가로 판단할 수 있다.

명령과 피드백이 CAN, CAN FD, 자동차 이더넷(Automotive Ethernet), EtherCAT 또는 다른 결정론적 네트워크(Deterministic Network)를 통해 전달될 수 있으므로 통신 설계(Communication Design) 역시 SbW 소프트웨어의 중요한 부분이다. 각 메시지는 지원되는 경우 카운터(Counter), 타임스탬프, 상태 정보(State Information), 오류 검출 필드(Error-Detection Field) 등 최신성과 무결성(Integrity)을 확인할 수 있는 충분한 정보를 포함해야 한다. 타임아웃 모니터링(Timeout Monitoring)은 정의된 고장 허용 시간 간격(Fault-Tolerant Time Interval) 내에서 통신 손실을 인식하도록 한다.

조향 상태 머신(Steering State Machine)은 초기화(Initialization), 대기(Standby), 활성 운전(Enabled Operation), 성능 저하 운전(Degraded Operation), 고장 처리(Fault Handling), 보정, 종료(Shutdown)를 조정한다. 소프트웨어 실행이 시작되었다는 이유만으로 조향 토크 또는 위치 제어가 활성화되어서는 안 된다. 차량 운동을 발생시킬 수 있는 운전 상태로 전환하기 전에 센서 타당성, 통신 가용성(Communication Availability), 액추에이터 준비 상태(Actuator Readiness), 전원 상태(Power Condition), 보정 유효성(Calibration Validity), 안전 승인(Safety Authorization)을 확인해야 한다.

전원 재인가(Power Cycling) 이후에는 조향 위치가 알려지지 않았거나 오프셋이 발생할 수 있으므로 시동 동작(Startup Behavior)에 특별한 주의가 필요하다. 시스템은 하드웨어 설계에 따라 절대 위치 센싱(Absolute Position Sensing), 저장된 보정 정보, 기계적 기준점(Mechanical Reference), 제어된 원점 복귀(Controlled Homing)를 사용할 수 있다. 소프트웨어는 초기화 과정에서 제어되지 않은 조향 움직임을 방지하고 정상적인 명령 추종을 활성화하기 전에 해석된 조향 위치가 이중화 센서와 일치하는지 확인해야 한다.

고장 처리(Fault Handling)는 일시적 고장(Transient Failure), 복구 가능한 고장(Recoverable Failure), 성능 저하 고장(Degraded Failure), 치명적 고장(Critical Failure)을 구분해야 한다. 대표적인 사례로 센서 불일치, 모터 과전류(Motor Overcurrent), 액추에이터 과열(Actuator Overtemperature), 통신 타임아웃, 과도한 추종 오차(Excessive Tracking Error), 잘못된 보정, 예상하지 못한 조향 움직임, 제어기 워치독 만료(Controller Watchdog Expiration)가 있다. 각 고장은 명령 제한, 조향 변화율 감소, 이중화 채널 전환(Redundant-Channel Takeover), 제어된 중앙 복귀(Controlled Centering), 성능 저하 운전 또는 차량 안전 정지(Safe Vehicle Stop)와 같은 정의된 대응 동작으로 연결되어야 한다.

안전 모니터(Safety Monitor)는 비정상적인 동작을 탐지할 수 있도록 정상 조향 제어기(Nominal Steering Controller)로부터 충분한 독립성을 가지고 동작해야 한다. 명령 조향각과 측정 조향각, 최대 조향 변화율, 액추에이터 전류, 실행 타이밍(Execution Timing), 통신 상태, 제어 상태 전환(Control-State Transition)을 감독할 수 있다. 독립적인 모니터링은 하나의 소프트웨어 결함이 잘못된 명령을 생성하는 동시에 그 결과로 발생한 조향 동작까지 정상으로 판단할 가능성을 줄인다.

이중화 SbW 아키텍처(Redundant SbW Architecture)는 이중 프로세싱 채널(Dual Processing Channel), 중복 센서(Duplicated Sensor), 분리된 전원 경로(Separate Power Path), 다중 조향 액추에이터(Multiple Steering Actuator)를 포함할 수 있다. 소프트웨어는 채널 건전성(Channel Health)을 조정하고 중요한 상태를 교차 확인(Cross-Check)하며 장애 전환(Failover)을 위한 결정론적 규칙을 정의해야 한다. 공통 원인 고장(Common-Cause Failure)과 불일치 처리를 고려할 때만 이중화가 의미가 있으며, 독립적인 감독 없이 동일한 계산을 단순히 복제하는 것만으로는 자동적으로 고장 허용 조향(Fault-Tolerant Steering)을 제공하지 않는다.

타이밍 결정성(Timing Determinism)은 조향 안정성과 안전성에 직접적인 영향을 준다. 센서 획득(Sensor Acquisition), 제어 연산(Control Computation), 통신, 액추에이터 갱신(Actuator Update), 진단 감독(Diagnostic Supervision)은 제한된 주기와 지연 시간 내에서 실행되어야 한다. 타임스탬프가 포함된 데이터는 실제 물리적 조향 오차와 지연된 측정값을 구분하는 데 도움이 된다. 실시간 스케줄링(Real-Time Scheduling)은 로깅, 진단 또는 비핵심 통신이 허용된 실행 시간 범위를 초과하여 조향 제어 루프를 지연시키지 않도록 적절한 우선순위를 할당해야 한다.

조향 피드백(Steering Feedback)은 상위 차량 소프트웨어에도 제공되어야 한다. 측정 바퀴 조향각, 등가 조향각, 액추에이터 상태, 조향 변화율, 고장 상태, 유효성 정보(Validity Information)는 위치추정(Localization), 모션 제어(Motion Control), 궤적 추종(Trajectory Tracking), 안전 기능에서 사용할 수 있다. 값과 함께 품질 상태(Quality State)를 제공하면 상위 계층이 단순히 수치가 존재한다는 이유만으로 해당 조향 측정값을 신뢰할 수 있다고 가정하는 것을 방지할 수 있다.

보정 관리(Calibration Management)는 범용 SbW 소프트웨어와 실제 조향 기구를 연결한다. 영점 오프셋(Zero Offset), 조향비(Steering Ratio), 바퀴 조향각 한계(Wheel-Angle Limit), 액추에이터 이동 범위, 센서 스케일링(Sensor Scaling), 비선형 링크 맵(Nonlinear Linkage Map), 제어기 게인, 진단 임계값(Diagnostic Threshold), 변화율 제약(Rate Constraint)은 버전 관리되는 파라미터(Version-Controlled Parameter)로 저장해야 한다. 소프트웨어는 시동 시 파라미터 무결성을 검증하고 보정 버전, 소프트웨어 릴리스(Software Release), 차량 구성(Vehicle Configuration) 사이의 추적성(Traceability)을 유지해야 한다.

진단 및 로깅(Diagnostics and Logging)은 비정상적인 조향 이벤트를 재구성할 수 있을 만큼 충분한 상황 정보를 기록해야 한다. 명령원, 요구 조향각, 제한된 목표값(Limited Target), 측정 위치, 액추에이터 전류, 제어기 상태, 고장 플래그(Fault Flag), 타임스탬프, 통신 상태, 안전 상태 전환(Safety Transition)은 특히 유용하다. 고속 신호(High-Rate Signal)는 순환 버퍼(Circular Buffer)에 저장하여 과도한 데이터를 지속적으로 기록하지 않고도 고장 발생 직전과 직후의 데이터를 보존할 수 있다.

소프트웨어 검증(Software Verification)은 변환, 제한, 상태 전환, 센서 타당성, 명령 중재, 타임아웃 처리, 고장 대응을 검증하는 단위 시험(Unit Test)부터 시작해야 한다. 소프트웨어 인 더 루프 시험(Software-in-the-Loop Testing)은 시뮬레이션된 차량 및 액추에이터 모델을 이용하여 폐루프 조향 동작을 평가할 수 있다. 하드웨어 인 더 루프 시험(Hardware-in-the-Loop Testing)은 반복 가능한 조건에서 통신 손실, 센서 오프셋, 액추에이터 지연, 전류 제한, 프로세서 타이밍 고장(Processor Timing Fault), 이중화 채널 불일치를 주입할 수 있다.

차량 수준 검증(Vehicle-Level Validation)은 액추에이터 추종 정확도뿐만 아니라 그 결과로 나타나는 차량 운동도 확인해야 한다. 시험에는 직선 유지(Straight-Line Holding), 완만한 선회, 최대 조향 명령, 급격한 방향 반전(Rapid Reversal), 저속 및 고속 운전, 명령원 전환, 시동 및 종료, 성능 저하 모드, 비상 대응(Emergency Response)이 포함되어야 한다. 측정된 요 레이트와 궤적 곡률(Trajectory Curvature)을 조향 운동학 모델(Steering Kinematic Model)에서 예측한 값과 비교하여 시스템 수준의 불일치(System-Level Inconsistency)를 검출할 수 있다.

전자식 조향 명령은 순수 기계식 조향 연결에는 존재하지 않는 공격 표면(Attack Surface)을 만들기 때문에 사이버보안(Cybersecurity)을 고려해야 한다. 명령 인터페이스는 인가되지 않은 명령원(Unauthorized Source)을 제한하고, 통신은 적절한 인증성(Authenticity)과 무결성 보호(Integrity Protection)를 제공해야 하며, 진단 또는 서비스 기능이 조향 권한 제어(Steering Authority Control)를 우회하지 못하도록 해야 한다. 보안 메커니즘(Security Mechanism)은 통신 또는 인증 실패 상황에서도 결정론적 안전 동작(Deterministic Safety Behavior)을 유지하도록 설계해야 한다.

로봇 제어 스택(Robot Control Stack)에서 SbW는 모션 제어 알고리즘(Motion-Control Algorithm)과 하드웨어별 조향 기구(Hardware-Specific Steering Mechanism) 사이에 안정적인 인터페이스를 제공해야 한다. 경로 추종 소프트웨어(Path-Tracking Software)는 모터 엔코더 카운트나 랙 기하학을 알 필요 없이 차량 수준의 조향 동작을 요구할 수 있으며, SbW 계층은 변환, 폐루프 구동, 모니터링, 고장 관리를 담당한다. 이러한 분리는 조향 하드웨어와 상위 자율주행 소프트웨어(Autonomy Software)가 상호 의존성을 줄인 상태에서 독립적으로 발전할 수 있도록 한다.

따라서 강건한 SbW 소프트웨어 설계(Robust SbW Software Design)는 명령 검증(Command Validation), 권한 관리(Authority Management), 조향 운동학(Steering Kinematics), 액추에이터 제어, 이중화 센싱, 결정론적 통신(Deterministic Communication), 상태 관리(State Management), 안전 모니터링(Safety Monitoring), 진단, 보정, 검증을 통합해야 한다. 이러한 요소를 서로 분리된 기능이 아니라 상호 연계된 계층으로 구성하면 전자식 조향은 정상 운전, 성능 저하 조건, 시스템 고장 상황에서도 예측 가능한 동작(Predictable Behavior)을 유지하면서 정밀한 제어 성능을 제공할 수 있다.

##  

## 04.04 Steering Angle Sensor Processing and Calibration [w/Code]

![](images/image4.png){width="7.268055555555556in" height="7.268055555555556in"}

Steering angle sensing provides the feedback required to determine the actual orientation of a vehicle or robot steering mechanism. Depending on the platform, the measured quantity may represent steering-wheel angle, motor-shaft position, rack displacement, axle steering angle, or individual road-wheel angle. Control software must therefore identify the physical meaning of each signal before using it for closed-loop steering, vehicle-state estimation, or diagnostics.

Common steering-angle sensing technologies include magnetic absolute encoders, optical encoders, Hall-effect sensors, potentiometric sensors, resolver-based devices, and actuator-integrated position sensors. Some provide an absolute angle immediately after power-up, while incremental devices require a known reference or homing procedure. Sensor selection influences initialization strategy, achievable resolution, measurement range, interface design, fault detection, and calibration requirements.

The sensor-processing pipeline begins with acquisition of raw electrical or digital measurements. Depending on the interface, input may consist of ADC counts, encoder pulses, absolute digital position words, PWM duty cycles, SENT data, CAN messages, or other encoded values. The software should validate communication status and raw signal range before applying scaling so that invalid electrical measurements are not converted into apparently plausible steering angles.

Raw measurements are converted into engineering units using sensor-specific scaling parameters. A simple linear conversion can be expressed as angle=gain×raw_value+offset, although practical steering mechanisms frequently require additional transformations. Encoder counts may first be converted into motor-shaft angle, followed by gear-ratio and linkage transformations that produce rack position or road-wheel angle. Each conversion stage should preserve units and sign conventions explicitly.

Zero-offset calibration establishes the measurement corresponding to the mechanical straight-ahead steering position. Manufacturing tolerances, sensor mounting variation, linkage assembly, and replacement components can shift the electrical zero away from the true vehicle center. The calibration procedure determines this difference and stores a zero-offset parameter so that a physically centered steering mechanism is reported consistently as the defined software zero position.

Center calibration should use a reliable mechanical or geometric reference rather than assuming that a raw sensor midpoint represents straight driving. Depending on the platform, technicians may align wheels using fixtures, mechanical stops, wheel-alignment equipment, or measured vehicle geometry. Autonomous platforms can supplement static calibration with motion-based observations, but such methods should not replace a defined reference procedure when safety-critical steering accuracy is required.

Steering polarity must also be verified during calibration. The software convention may define left steering as positive and right steering as negative, while the physical sensor or actuator can increase in the opposite direction. A polarity parameter or explicit coordinate transformation should normalize the measurement before it reaches higher-level software. Incorrect polarity can transform otherwise valid feedback into a destabilizing closed-loop control error.

Steering systems frequently contain nonlinear relationships between sensor position and actual road-wheel angle. Rack-and-pinion geometry, steering arms, tie rods, suspension geometry, and actuator linkages can cause the effective steering ratio to change across the travel range. A single constant ratio may therefore be insufficient. Lookup tables, piecewise-linear interpolation, polynomial functions, or experimentally identified mappings can represent this nonlinear conversion more accurately.

Left and right steering characteristics may not be perfectly symmetric. Mechanical tolerances, linkage geometry, stops, compliance, and sensor mounting can produce different maximum angles or conversion curves in each direction. Calibration software should permit asymmetric parameters where necessary rather than forcing mirrored behavior. Independent left and right calibration data can substantially improve wheel-angle estimation near large steering angles.

Filtering is required because steering sensor measurements contain electrical noise, quantization effects, mechanical vibration, and communication jitter. Low-pass filters, moving-average filters, or appropriately designed digital filters can reduce these disturbances, but excessive filtering introduces phase delay. Since steering feedback participates in real-time control, filter bandwidth should be selected according to actuator dynamics, control-loop frequency, vehicle speed, and required fault-detection response.

Angle-rate estimation can be derived from successive steering-angle measurements when a direct velocity sensor is unavailable. Simple differentiation amplifies measurement noise, so filtered differentiation or state-estimation methods are generally preferable. The estimated steering rate is useful for actuator control, steering-rate limitation, dynamic monitoring, and diagnostics. Accurate timestamps are necessary because variation in sampling intervals directly affects numerical derivative accuracy.

Angle unwrapping may be required for sensors that represent position cyclically or for steering mechanisms whose motor shaft rotates multiple revolutions. The software must distinguish a genuine wraparound transition from a large physical steering movement. Multi-turn tracking can combine absolute single-turn position with revolution counters or retained state, but startup behavior must account for whether previous revolution information can be trusted after a power interruption.

Sensor plausibility monitoring evaluates whether a measurement is physically possible and consistent with recent history. Typical checks include minimum and maximum angle, maximum rate of change, discontinuity detection, frozen-signal detection, and communication timeout. A value that lies within the legal angle range can still be faulty if it jumps faster than the steering mechanism can physically move or remains unchanged despite verified actuator motion.

Redundant steering sensors allow stronger fault detection by comparing independent measurements of the same or related physical quantity. Dual angle sensors may use different electrical channels, sensing technologies, or power supplies to reduce common-cause failures. Software can evaluate absolute disagreement, rate disagreement, correlation, and direction consistency. Persistent disagreement beyond calibrated thresholds should generate an explicit diagnostic state.

Analytical redundancy can supplement duplicated sensors by comparing measurements across different parts of the steering mechanism. Motor encoder angle, actuator position, rack displacement, road-wheel angle, commanded steering, and vehicle yaw response are physically related. A model-based residual between these signals can reveal sensor drift, mechanical disconnection, linkage deformation, excessive backlash, or actuator tracking errors that may not be detected by simple range checking.

Calibration data should be separated from executable control logic and managed as traceable configuration information. Typical parameters include zero offset, sensor gain, polarity, minimum and maximum raw values, mechanical angle limits, steering ratio, nonlinear mapping tables, filter constants, diagnostic thresholds, and temperature compensation coefficients. Parameter versioning allows calibration changes to be associated with a specific vehicle, steering hardware revision, and software release.

Calibration integrity must be verified whenever parameters are loaded. Checksums, range validation, version compatibility, and default-value handling can prevent corrupted or incompatible data from producing incorrect steering feedback. Safety-critical systems should avoid silently replacing invalid calibration with generic values if doing so could create an unsafe steering interpretation. Instead, invalid calibration should produce a defined startup restriction or diagnostic fault.

Temperature can influence magnetic sensors, analog electronics, mechanical dimensions, and steering friction. Where characterization shows meaningful temperature dependency, calibration can include compensation based on measured sensor or actuator temperature. Compensation should be derived from test data rather than assumed theoretically, and its valid temperature range should be defined so that extrapolation outside characterized conditions does not generate misleading corrections.

Calibration procedures should distinguish factory calibration, service calibration, and runtime adaptation. Factory calibration establishes baseline geometry and sensor characteristics during manufacturing. Service calibration restores correct parameters after sensor, actuator, linkage, or alignment work. Runtime adaptation may compensate slowly varying offsets or mechanical changes, but adaptive values should be bounded and monitored so that genuine hardware faults are not incorrectly learned as normal behavior.

The processed steering-angle signal should include quality information in addition to the numerical angle. Useful status fields include validity, calibration state, sensor health, redundancy agreement, timeout state, estimated uncertainty, and degradation status. Higher-level controllers can then determine whether the measurement is suitable for normal operation, restricted operation, or only diagnostic use instead of treating every available numeric value as equally trustworthy.

Diagnostics should preserve raw and processed data when abnormal behavior occurs. Raw sensor value, converted angle, filtered angle, zero offset, calibration version, actuator command, steering rate, redundant measurement, vehicle speed, yaw rate, timestamps, and fault flags provide valuable context for root-cause analysis. Capturing both pre-fault and post-fault samples helps distinguish sensor failure from mechanical or communication problems.

Verification should test the complete processing chain rather than only individual conversion functions. Unit tests can cover scaling, polarity, offset correction, interpolation, filtering, unwrapping, saturation, and fault thresholds. Software-in-the-loop testing can inject noise, drift, discontinuities, and timing variation, while hardware-in-the-loop testing can evaluate actual sensor interfaces, communication faults, power cycling, and redundant-channel disagreement.

Vehicle-level calibration validation should compare processed steering information with independent geometric or motion references. Tests can evaluate straight-ahead alignment, symmetric left and right steering, maximum travel, repeated center return, low-speed circular driving, and measured yaw response. Repetition is important because backlash and hysteresis may cause the same commanded position to produce different measurements depending on the direction of approach.

Within the steering-control architecture, sensor processing and calibration form the measurement boundary between physical steering hardware and software control functions. Low-level interfaces acquire raw signals, calibration converts them into physical coordinates, filtering and estimation improve usability, and diagnostic logic determines confidence. Higher-level controllers should consume this standardized steering state rather than directly interpreting hardware-specific sensor values.

A robust steering-angle processing and calibration design therefore combines reliable acquisition, explicit unit conversion, zero and polarity correction, nonlinear mapping, filtering, rate estimation, redundancy checking, parameter traceability, calibration integrity, and systematic validation. This structure provides accurate and trustworthy steering feedback for Ackermann steering, four-wheel steering, steer-by-wire control, path tracking, safety supervision, and vehicle diagnostics.

조향각 센싱(Steering Angle Sensing)은 차량 또는 로봇 조향 기구의 실제 방향을 판단하는 데 필요한 피드백(Feedback)을 제공한다. 플랫폼에 따라 측정 대상은 스티어링 휠 각도(Steering-Wheel Angle), 모터 축 위치(Motor-Shaft Position), 랙 변위(Rack Displacement), 차축 조향각(Axle Steering Angle), 개별 로드 휠 각도(Individual Road-Wheel Angle)를 나타낼 수 있다. 따라서 제어 소프트웨어는 폐루프 조향(Closed-Loop Steering), 차량 상태 추정(Vehicle-State Estimation), 진단(Diagnostics)에 신호를 사용하기 전에 각 신호가 나타내는 물리적 의미를 명확하게 식별해야 한다.

일반적인 조향각 센싱 기술(Steering-Angle Sensing Technology)에는 자기식 절대 엔코더(Magnetic Absolute Encoder), 광학식 엔코더(Optical Encoder), 홀 효과 센서(Hall-Effect Sensor), 전위차계 센서(Potentiometric Sensor), 리졸버 기반 장치(Resolver-Based Device), 액추에이터 통합 위치 센서(Actuator-Integrated Position Sensor)가 포함된다. 일부 센서는 전원 인가 직후 절대각(Absolute Angle)을 제공하지만 증분형 장치(Incremental Device)는 알려진 기준 위치 또는 원점 복귀 절차(Homing Procedure)가 필요하다. 센서 선택은 초기화 전략, 구현 가능한 분해능(Resolution), 측정 범위, 인터페이스 설계, 고장 검출, 보정 요구사항에 영향을 준다.

센서 처리 파이프라인(Sensor-Processing Pipeline)은 원시 전기 또는 디지털 측정값(Raw Electrical or Digital Measurement)을 획득하는 과정에서 시작한다. 인터페이스에 따라 입력값은 ADC 카운트(ADC Count), 엔코더 펄스(Encoder Pulse), 절대 디지털 위치 워드(Absolute Digital Position Word), PWM 듀티 사이클(PWM Duty Cycle), SENT 데이터, CAN 메시지 또는 기타 인코딩된 값으로 구성될 수 있다. 소프트웨어는 스케일링(Scaling)을 적용하기 전에 통신 상태와 원시 신호 범위를 검증하여 잘못된 전기적 측정값이 정상적으로 보이는 조향각으로 변환되는 것을 방지해야 한다.

원시 측정값은 센서별 스케일링 파라미터(Sensor-Specific Scaling Parameter)를 사용하여 공학 단위(Engineering Unit)로 변환된다. 단순한 선형 변환은 angle=gain×raw_value+offset으로 표현할 수 있지만 실제 조향 기구에서는 추가적인 변환이 필요한 경우가 많다. 엔코더 카운트는 먼저 모터 축 각도(Motor-Shaft Angle)로 변환한 후 기어비(Gear Ratio)와 링크 기구 변환(Linkage Transformation)을 거쳐 랙 위치 또는 로드 휠 각도로 변환할 수 있다. 각 변환 단계에서는 단위(Unit)와 부호 규약(Sign Convention)을 명시적으로 유지해야 한다.

영점 오프셋 보정(Zero-Offset Calibration)은 기계적인 직진 조향 위치(Mechanical Straight-Ahead Steering Position)에 대응하는 측정값을 설정한다. 제조 공차(Manufacturing Tolerance), 센서 장착 편차(Sensor Mounting Variation), 링크 조립(Linkage Assembly), 교체 부품은 전기적 영점(Electrical Zero)을 실제 차량 중심 위치에서 벗어나게 할 수 있다. 보정 절차는 이러한 차이를 결정하고 영점 오프셋 파라미터를 저장하여 물리적으로 중앙에 정렬된 조향 기구가 소프트웨어에서 정의한 영점 위치로 일관되게 보고되도록 한다.

중앙 위치 보정(Center Calibration)은 원시 센서의 중간값이 직진 주행을 의미한다고 가정하기보다 신뢰할 수 있는 기계적 또는 기하학적 기준(Mechanical or Geometric Reference)을 사용해야 한다. 플랫폼에 따라 작업자는 지그(Fixture), 기계적 스토퍼(Mechanical Stop), 휠 얼라인먼트 장비(Wheel-Alignment Equipment), 측정된 차량 기하학을 사용하여 바퀴를 정렬할 수 있다. 자율 플랫폼은 정적 보정(Static Calibration)을 운동 기반 관측(Motion-Based Observation)으로 보완할 수 있지만 안전에 중요한 조향 정확도가 요구되는 경우 이러한 방법이 정의된 기준 절차를 대체해서는 안 된다.

조향 극성(Steering Polarity) 역시 보정 과정에서 검증해야 한다. 소프트웨어 규약은 좌측 조향을 양수(Positive), 우측 조향을 음수(Negative)로 정의할 수 있지만 실제 센서 또는 액추에이터는 반대 방향으로 값이 증가할 수 있다. 극성 파라미터(Polarity Parameter) 또는 명시적인 좌표 변환(Coordinate Transformation)을 사용하여 측정값이 상위 소프트웨어로 전달되기 전에 정규화해야 한다. 잘못된 극성은 정상적인 피드백을 폐루프 제어를 불안정하게 만드는 제어 오차(Control Error)로 변환할 수 있다.

조향 시스템은 센서 위치와 실제 로드 휠 각도 사이에 비선형 관계(Nonlinear Relationship)를 갖는 경우가 많다. 랙 앤 피니언 기하학(Rack-and-Pinion Geometry), 스티어링 암(Steering Arm), 타이로드(Tie Rod), 서스펜션 기하학(Suspension Geometry), 액추에이터 링크 기구(Actuator Linkage)는 전체 이동 범위에서 유효 조향비(Effective Steering Ratio)를 변화시킬 수 있다. 따라서 하나의 일정한 조향비만으로는 충분하지 않을 수 있으며, 룩업 테이블(Lookup Table), 구간별 선형 보간(Piecewise-Linear Interpolation), 다항 함수(Polynomial Function), 실험적으로 식별된 매핑(Experimentally Identified Mapping)을 사용하여 이러한 비선형 변환을 더욱 정확하게 표현할 수 있다.

좌측과 우측 조향 특성(Steering Characteristic)이 완벽하게 대칭적이지 않을 수도 있다. 기계적 공차, 링크 기하학, 스토퍼(Stop), 컴플라이언스(Compliance), 센서 장착 상태에 따라 각 방향에서 서로 다른 최대 조향각 또는 변환 곡선(Conversion Curve)이 발생할 수 있다. 따라서 필요한 경우 보정 소프트웨어는 강제로 대칭 동작을 적용하기보다 비대칭 파라미터(Asymmetric Parameter)를 허용해야 한다. 독립적인 좌우 보정 데이터는 큰 조향각 영역에서 바퀴 조향각 추정 정확도를 크게 향상시킬 수 있다.

조향 센서 측정값에는 전기적 노이즈(Electrical Noise), 양자화 효과(Quantization Effect), 기계적 진동(Mechanical Vibration), 통신 지터(Communication Jitter)가 포함되므로 필터링(Filtering)이 필요하다. 저역통과 필터(Low-Pass Filter), 이동평균 필터(Moving-Average Filter), 적절하게 설계된 디지털 필터(Digital Filter)를 사용하여 이러한 외란을 줄일 수 있지만 과도한 필터링은 위상 지연(Phase Delay)을 발생시킨다. 조향 피드백은 실시간 제어에 사용되므로 필터 대역폭(Filter Bandwidth)은 액추에이터 동역학, 제어 루프 주파수(Control-Loop Frequency), 차량 속도, 요구되는 고장 검출 응답에 따라 결정해야 한다.

직접적인 속도 센서가 없는 경우 연속된 조향각 측정값으로부터 조향각 변화율 추정(Angle-Rate Estimation)을 수행할 수 있다. 단순 미분(Simple Differentiation)은 측정 노이즈를 증폭시키므로 일반적으로 필터링된 미분(Filtered Differentiation) 또는 상태 추정 방법(State-Estimation Method)을 사용하는 것이 바람직하다. 추정된 조향 변화율은 액추에이터 제어, 조향 변화율 제한, 동적 모니터링(Dynamic Monitoring), 진단에 유용하다. 샘플링 간격의 변화가 수치 미분 정확도에 직접 영향을 주므로 정확한 타임스탬프가 필요하다.

위치를 주기적으로 표현하는 센서 또는 모터 축이 여러 번 회전하는 조향 기구에서는 각도 언래핑(Angle Unwrapping)이 필요할 수 있다. 소프트웨어는 실제 랩어라운드 전환(Wraparound Transition)과 큰 물리적 조향 움직임을 구분해야 한다. 다회전 추적(Multi-Turn Tracking)은 절대 단일 회전 위치(Absolute Single-Turn Position)를 회전 카운터(Revolution Counter) 또는 유지된 상태(Retained State)와 결합하여 구현할 수 있지만 시동 과정에서는 전원 차단 이후 이전 회전 정보를 신뢰할 수 있는지를 고려해야 한다.

센서 타당성 모니터링(Sensor Plausibility Monitoring)은 측정값이 물리적으로 가능한지 그리고 최근 측정 이력과 일관되는지를 평가한다. 대표적인 검사에는 최소 및 최대 조향각, 최대 변화율, 불연속 검출(Discontinuity Detection), 신호 고정 검출(Frozen-Signal Detection), 통신 타임아웃이 포함된다. 값이 허용된 조향각 범위 안에 있더라도 조향 기구가 물리적으로 움직일 수 있는 속도보다 빠르게 변화하거나 검증된 액추에이터 움직임에도 불구하고 값이 변하지 않는다면 해당 신호는 고장일 수 있다.

이중화 조향 센서(Redundant Steering Sensor)는 동일하거나 관련된 물리량에 대한 독립적인 측정값을 비교하여 더욱 강력한 고장 검출을 가능하게 한다. 이중 각도 센서(Dual Angle Sensor)는 공통 원인 고장(Common-Cause Failure)을 줄이기 위해 서로 다른 전기 채널, 센싱 기술 또는 전원을 사용할 수 있다. 소프트웨어는 절대값 불일치(Absolute Disagreement), 변화율 불일치(Rate Disagreement), 상관관계, 방향 일관성(Direction Consistency)을 평가할 수 있다. 보정된 임계값을 초과하는 지속적인 불일치는 명시적인 진단 상태(Diagnostic State)를 발생시켜야 한다.

해석적 이중화(Analytical Redundancy)는 조향 기구의 서로 다른 부분에서 얻은 측정값을 비교하여 중복 센서를 보완할 수 있다. 모터 엔코더 각도, 액추에이터 위치, 랙 변위, 로드 휠 각도, 명령 조향값, 차량 요 응답(Vehicle Yaw Response)은 물리적으로 서로 연관되어 있다. 이러한 신호 사이의 모델 기반 잔차(Model-Based Residual)를 이용하면 단순한 범위 검사만으로 탐지하기 어려운 센서 드리프트(Sensor Drift), 기계적 분리(Mechanical Disconnection), 링크 변형(Linkage Deformation), 과도한 백래시(Excessive Backlash), 액추에이터 추종 오차를 검출할 수 있다.

보정 데이터(Calibration Data)는 실행 가능한 제어 로직(Executable Control Logic)과 분리하여 추적 가능한 구성 정보(Traceable Configuration Information)로 관리해야 한다. 대표적인 파라미터에는 영점 오프셋, 센서 게인(Sensor Gain), 극성, 최소 및 최대 원시값, 기계적 조향각 한계(Mechanical Angle Limit), 조향비, 비선형 매핑 테이블(Nonlinear Mapping Table), 필터 상수(Filter Constant), 진단 임계값, 온도 보상 계수(Temperature Compensation Coefficient)가 포함된다. 파라미터 버전 관리(Parameter Versioning)를 통해 보정 변경 사항을 특정 차량, 조향 하드웨어 개정판(Hardware Revision), 소프트웨어 릴리스와 연계할 수 있다.

보정 파라미터가 로드될 때마다 보정 무결성(Calibration Integrity)을 검증해야 한다. 체크섬(Checksum), 범위 검증(Range Validation), 버전 호환성(Version Compatibility), 기본값 처리(Default-Value Handling)를 통해 손상되었거나 호환되지 않는 데이터가 잘못된 조향 피드백을 생성하는 것을 방지할 수 있다. 안전 중요 시스템(Safety-Critical System)에서는 잘못된 보정을 일반적인 기본값으로 조용히 대체하는 것이 위험한 조향 해석을 발생시킬 수 있다면 이를 피해야 한다. 대신 잘못된 보정은 정의된 시동 제한(Startup Restriction) 또는 진단 고장을 발생시켜야 한다.

온도는 자기 센서(Magnetic Sensor), 아날로그 전자회로(Analog Electronics), 기계적 치수(Mechanical Dimension), 조향 마찰(Steering Friction)에 영향을 줄 수 있다. 특성 평가(Characterization)를 통해 의미 있는 온도 의존성이 확인된 경우 측정된 센서 또는 액추에이터 온도를 기반으로 보상(Compensation)을 적용할 수 있다. 온도 보상은 이론적으로 가정하기보다 시험 데이터(Test Data)를 기반으로 도출해야 하며, 특성화된 범위를 벗어난 외삽(Extrapolation)이 잘못된 보정값을 생성하지 않도록 유효 온도 범위를 정의해야 한다.

보정 절차(Calibration Procedure)는 공장 보정(Factory Calibration), 서비스 보정(Service Calibration), 런타임 적응(Runtime Adaptation)을 구분해야 한다. 공장 보정은 제조 과정에서 기본 차량 기하학과 센서 특성을 설정한다. 서비스 보정은 센서, 액추에이터, 링크 기구 또는 얼라인먼트 작업 이후 올바른 파라미터를 복원한다. 런타임 적응은 서서히 변화하는 오프셋이나 기계적 변화를 보상할 수 있지만 실제 하드웨어 고장을 정상적인 동작으로 잘못 학습하지 않도록 적응값(Adaptive Value)의 범위를 제한하고 지속적으로 모니터링해야 한다.

처리된 조향각 신호(Processed Steering-Angle Signal)는 수치적인 조향각뿐만 아니라 품질 정보(Quality Information)도 포함해야 한다. 유용한 상태 필드에는 유효성(Validity), 보정 상태(Calibration State), 센서 건전성(Sensor Health), 이중화 일치 상태(Redundancy Agreement), 타임아웃 상태, 추정 불확실성(Estimated Uncertainty), 성능 저하 상태(Degradation Status)가 포함된다. 이를 통해 상위 제어기는 사용 가능한 모든 수치값을 동일하게 신뢰하지 않고 해당 측정값이 정상 운전, 제한 운전(Restricted Operation), 또는 진단 용도로 적합한지 판단할 수 있다.

진단(Diagnostics)은 비정상적인 동작이 발생했을 때 원시 데이터와 처리된 데이터를 함께 보존해야 한다. 원시 센서값, 변환된 조향각(Converted Angle), 필터링된 조향각(Filtered Angle), 영점 오프셋, 보정 버전, 액추에이터 명령, 조향 변화율, 이중화 측정값, 차량 속도, 요 레이트, 타임스탬프, 고장 플래그(Fault Flag)는 근본 원인 분석(Root-Cause Analysis)에 중요한 상황 정보를 제공한다. 고장 전후의 샘플을 모두 확보하면 센서 고장과 기계적 또는 통신 문제를 구분하는 데 도움이 된다.

검증(Verification)은 개별 변환 함수뿐만 아니라 전체 처리 체인(Processing Chain)을 시험해야 한다. 단위 시험(Unit Test)은 스케일링, 극성, 오프셋 보정, 보간(Interpolation), 필터링, 언래핑, 포화(Saturation), 고장 임계값을 검증할 수 있다. 소프트웨어 인 더 루프 시험(Software-in-the-Loop Testing)은 노이즈, 드리프트, 불연속, 타이밍 변화를 주입할 수 있으며, 하드웨어 인 더 루프 시험(Hardware-in-the-Loop Testing)은 실제 센서 인터페이스, 통신 고장, 전원 재인가, 이중화 채널 불일치를 평가할 수 있다.

차량 수준 보정 검증(Vehicle-Level Calibration Validation)은 처리된 조향 정보를 독립적인 기하학적 또는 운동 기준(Geometric or Motion Reference)과 비교해야 한다. 시험에서는 직진 중앙 정렬(Straight-Ahead Alignment), 대칭적인 좌우 조향, 최대 이동 범위(Maximum Travel), 반복적인 중앙 복귀(Repeated Center Return), 저속 원형 주행(Low-Speed Circular Driving), 측정된 요 응답을 평가할 수 있다. 백래시와 히스테리시스로 인해 동일한 명령 위치도 접근 방향에 따라 서로 다른 측정값을 생성할 수 있으므로 반복 시험(Repetition Test)이 중요하다.

조향 제어 아키텍처(Steering-Control Architecture)에서 센서 처리 및 보정은 실제 조향 하드웨어와 소프트웨어 제어 기능 사이의 측정 경계(Measurement Boundary)를 형성한다. 하위 인터페이스는 원시 신호를 획득하고, 보정은 이를 물리 좌표(Physical Coordinate)로 변환하며, 필터링과 추정(Estimation)은 신호의 활용성을 향상시키고, 진단 로직은 신뢰도(Confidence)를 결정한다. 상위 제어기는 하드웨어별 센서값을 직접 해석하기보다 이러한 표준화된 조향 상태(Standardized Steering State)를 사용해야 한다.

따라서 강건한 조향각 처리 및 보정 설계(Robust Steering-Angle Processing and Calibration Design)는 신뢰성 있는 신호 획득(Reliable Acquisition), 명확한 단위 변환(Unit Conversion), 영점 및 극성 보정, 비선형 매핑, 필터링, 변화율 추정(Rate Estimation), 이중화 검사(Redundancy Checking), 파라미터 추적성(Parameter Traceability), 보정 무결성, 체계적인 검증(Systematic Validation)을 통합해야 한다. 이러한 구조는 애커먼 조향(Ackermann Steering), 4륜 조향(Four-Wheel Steering), 전자식 조향(Steer-by-Wire Control), 경로 추종(Path Tracking), 안전 감독(Safety Supervision), 차량 진단(Vehicle Diagnostics)에 정확하고 신뢰할 수 있는 조향 피드백을 제공한다.

##  

## 04.05 Path Tracking Control: Pure Pursuit / Stanley [w/Code]

![](images/image5.png){width="7.268055555555556in" height="7.268055555555556in"}

Path tracking control converts a planned geometric path into steering commands that continuously guide a mobile robot or autonomous vehicle toward the desired trajectory. The controller compares the current vehicle pose with reference path information and calculates a steering action that reduces tracking error. Pure Pursuit and Stanley control are widely used geometric methods because they are computationally efficient and integrate naturally with Ackermann steering platforms.

A path-tracking system typically receives a sequence of reference poses or waypoints containing position, heading, curvature, and sometimes target velocity. Localization provides the current vehicle position and orientation, while vehicle interfaces provide measured speed and steering state. The controller combines these inputs at a periodic update rate and generates desired curvature or steering angle for the lower steering-control layer.

Coordinate transformation is an important first step because the global path and vehicle control calculations may use different reference frames. Nearby path points are transformed from the map or odometry frame into a vehicle-centered coordinate frame. This allows lateral displacement, forward distance, heading difference, and target-point geometry to be evaluated consistently without embedding global-map assumptions inside the steering algorithm.

Pure Pursuit is based on selecting a target point located a specified lookahead distance ahead of the vehicle and calculating the circular arc that connects the current vehicle pose to that point. Instead of directly minimizing the nearest lateral error, the algorithm attempts to steer toward a moving point on the reference path. This geometric interpretation makes Pure Pursuit relatively simple, intuitive, and suitable for real-time robotic control.

If the target point in the vehicle coordinate frame is located at longitudinal distance x and lateral distance y, its heading relative to the vehicle can be represented by alpha. For a lookahead distance Ld, the desired curvature is commonly expressed as kappa=2 sin(alpha)/Ld. For an Ackermann vehicle with wheelbase L, the equivalent steering command can then be calculated as delta=atan(L×kappa).

The lookahead distance strongly determines Pure Pursuit behavior. A short lookahead generally produces aggressive correction and can reduce local tracking error, but it may also cause steering oscillation or sensitivity to localization noise. A long lookahead generates smoother steering and improved stability but can cut corners or respond slowly to sharp curvature. Lookahead selection is therefore one of the principal calibration tasks.

Adaptive lookahead improves performance across different operating speeds. A common implementation defines the lookahead distance as a base value plus a term proportional to vehicle speed, such as Ld=L0+Kv. Low-speed operation then uses a shorter preview distance for maneuverability, while higher-speed operation uses a longer preview distance for smoother response. Minimum and maximum limits prevent unreasonable lookahead values.

Target-point selection requires careful software design because a discrete waypoint path may contain irregular spacing, loops, sharp corners, or points behind the vehicle. The controller should identify the nearest valid path region and search forward along path progress until the required lookahead distance is reached. Interpolation between path samples can provide a smoother target point than simply selecting the first waypoint exceeding the lookahead threshold.

Stanley control approaches path tracking differently by explicitly combining cross-track error and heading error. Cross-track error represents the lateral distance between the vehicle reference point and the desired path, while heading error represents the angular difference between the vehicle orientation and path tangent. The steering command corrects both errors simultaneously, providing direct convergence toward the reference path.

A commonly used Stanley relationship can be written as delta=psi_e+atan(k e_c/(v+epsilon)), where psi_e is heading error, e_c is signed cross-track error, k is a positive control gain, v is vehicle speed, and epsilon prevents numerical problems near zero speed. The heading term aligns the vehicle with the path, while the cross-track term drives the vehicle laterally toward it.

The Stanley gain determines how strongly lateral displacement influences steering. A large gain produces stronger correction but can cause oscillatory steering, particularly when localization noise or actuator delay is significant. A small gain produces smoother behavior but may allow larger tracking errors. Gain calibration should therefore consider speed range, wheelbase, steering dynamics, localization quality, path curvature, and control-loop frequency.

Low-speed behavior requires special treatment in Stanley control because the cross-track correction contains vehicle speed in the denominator. Without protection, the correction can become excessively large as speed approaches zero. A positive softening constant, minimum effective speed, steering saturation, or speed-dependent gain can maintain numerical stability. Reverse driving additionally requires consistent definitions of heading, path direction, and error signs.

Cross-track error must be signed rather than represented only as an absolute distance. The sign indicates which side of the reference path contains the vehicle and therefore determines the required correction direction. A consistent coordinate convention should define positive lateral error, positive heading error, and positive steering angle across localization, path representation, controller calculations, and steering actuator interfaces.

Pure Pursuit and Stanley therefore use different geometric information. Pure Pursuit primarily responds to the geometry of a forward target point and naturally includes path preview. Stanley directly combines current lateral and heading errors and can provide strong convergence to the reference path. Neither method is universally superior; their practical behavior depends on speed, path geometry, vehicle dynamics, localization accuracy, and parameter tuning.

The path-tracking software should isolate algorithm-specific calculations from common control infrastructure. Path management, coordinate transformation, localization validation, command constraints, actuator interfaces, diagnostics, and logging can remain shared while Pure Pursuit and Stanley are implemented as interchangeable controller modules. This structure supports controlled comparison and allows a platform to select an algorithm according to operating mode.

Steering commands produced by either controller must be constrained before actuation. Maximum steering angle, curvature, steering rate, and steering acceleration should respect vehicle geometry and actuator capability. Speed-dependent limits can further prevent aggressive steering at high velocity. Command filtering may reduce noise, but excessive smoothing can introduce tracking delay, so filtering should be coordinated with controller tuning and actuator bandwidth.

Path curvature can also be used as a feedforward component. When reference curvature is known, the controller can generate a nominal steering angle from the vehicle kinematic model and use Pure Pursuit or Stanley feedback to correct residual tracking errors. This combination reduces the amount of feedback action required on predictable curves and can improve tracking smoothness when the path representation contains reliable curvature information.

Localization quality directly limits path-tracking performance. Position jumps, heading noise, delayed pose estimates, or inconsistent timestamps can generate unnecessary steering corrections even when the control algorithm is mathematically correct. The controller should therefore check pose freshness and validity, align measurements to an appropriate time reference, and avoid producing unrestricted steering commands when localization confidence becomes insufficient.

Vehicle speed and steering measurements should likewise be synchronized with the pose used for control computation. At higher speeds, even moderate communication or processing delay can shift the effective vehicle position significantly between measurement and actuation. Timestamp-based synchronization, short-horizon pose prediction, or latency compensation can reduce this error and make controller behavior more consistent with the intended geometric model.

Path discontinuities require explicit handling. Sudden waypoint jumps, duplicated points, extremely short segments, discontinuous headings, or unrealistic curvature can produce large steering commands. Path preprocessing can remove duplicates, resample points at consistent spacing, smooth geometry, calculate tangent directions, and estimate curvature. The tracking controller should still validate incoming path data because preprocessing cannot guarantee valid runtime inputs.

Controller state management should define behavior when no path exists, the vehicle reaches the path end, localization is invalid, speed is below a controllable threshold, or the steering system reports a fault. Rather than continuing to use stale targets, the controller should enter a defined hold, stop, or invalid-output state. Explicit validity information should accompany steering requests transmitted to lower control layers.

Diagnostics should monitor cross-track error, heading error, selected target point, lookahead distance, requested curvature, steering command, measured steering angle, vehicle speed, localization status, and command saturation. Persistent large error can indicate poor tuning, actuator limitations, localization problems, path defects, excessive speed, or unexpected tire slip. Logging these variables makes field behavior reproducible and supports systematic calibration.

Software-in-the-loop testing should evaluate both algorithms on straight paths, constant-radius curves, S-curves, sharp transitions, sparse waypoints, noisy localization, and different speeds. Parameter sweeps can quantify sensitivity to lookahead distance and Stanley gain. Hardware-in-the-loop testing can add steering actuator delay, angle saturation, communication latency, and sensor noise before the controller is deployed on the physical vehicle.

Vehicle testing should measure more than whether the robot completes a route. Useful metrics include mean and maximum cross-track error, heading error, steering-rate activity, command saturation frequency, settling behavior, overshoot, and path-completion consistency. Tests should cover forward and reverse operation where supported, low-speed tight turns, higher-speed smooth curves, transitions, and disturbances representative of the intended operating environment.

A robust path-tracking architecture therefore combines reliable path representation, synchronized localization, coordinate transformation, geometric error calculation, configurable Pure Pursuit or Stanley control, steering constraints, diagnostics, and systematic validation. These controllers form the software bridge between planned trajectories and steering actuation, enabling Ackermann and related mobile robot platforms to follow reference paths accurately while maintaining predictable real-time behavior.

경로 추종 제어(Path Tracking Control)는 계획된 기하학적 경로(Geometric Path)를 이동 로봇(Mobile Robot) 또는 자율주행 차량(Autonomous Vehicle)이 목표 궤적을 지속적으로 따라가도록 하는 조향 명령(Steering Command)으로 변환한다. 제어기는 현재 차량 자세(Current Vehicle Pose)와 기준 경로 정보(Reference Path Information)를 비교하여 추종 오차(Tracking Error)를 감소시키는 조향 동작을 계산한다. 퓨어 퍼슈트(Pure Pursuit)와 스탠리 제어(Stanley Control)는 계산 효율이 높고 애커먼 조향(Ackermann Steering) 플랫폼과 자연스럽게 통합할 수 있어 널리 사용되는 기하학적 제어 방법(Geometric Method)이다.

경로 추종 시스템(Path-Tracking System)은 일반적으로 위치(Position), 헤딩(Heading), 곡률(Curvature), 경우에 따라 목표 속도(Target Velocity)를 포함하는 일련의 기준 자세(Reference Pose) 또는 웨이포인트(Waypoint)를 입력받는다. 위치추정(Localization)은 현재 차량 위치와 방향을 제공하고 차량 인터페이스(Vehicle Interface)는 측정 속도와 조향 상태를 제공한다. 제어기는 이러한 입력을 주기적으로 결합하여 하위 조향 제어 계층(Lower Steering-Control Layer)에 전달할 목표 곡률 또는 조향각을 생성한다.

좌표 변환(Coordinate Transformation)은 전역 경로(Global Path)와 차량 제어 계산이 서로 다른 기준 좌표계(Reference Frame)를 사용할 수 있기 때문에 중요한 첫 번째 단계이다. 인접한 경로점은 맵(Map) 또는 오도메트리 좌표계(Odometry Frame)에서 차량 중심 좌표계(Vehicle-Centered Coordinate Frame)로 변환된다. 이를 통해 전역 지도에 대한 가정을 조향 알고리즘 내부에 포함하지 않고 횡방향 변위(Lateral Displacement), 전방 거리(Forward Distance), 헤딩 차이(Heading Difference), 목표점 기하학(Target-Point Geometry)을 일관되게 계산할 수 있다.

퓨어 퍼슈트(Pure Pursuit)는 차량 전방의 지정된 전방주시 거리(Lookahead Distance)에 위치한 목표점(Target Point)을 선택하고 현재 차량 자세에서 해당 점까지 연결하는 원호(Circular Arc)를 계산하는 방식에 기반한다. 가장 가까운 횡방향 오차를 직접 최소화하는 대신 기준 경로 위에서 이동하는 목표점을 향하도록 조향한다. 이러한 기하학적 해석은 퓨어 퍼슈트를 비교적 단순하고 직관적으로 만들며 실시간 로봇 제어(Real-Time Robotic Control)에 적합하게 한다.

차량 좌표계에서 목표점이 종방향 거리 x와 횡방향 거리 y에 위치하면 차량에 대한 목표점의 상대 방향을 alpha로 나타낼 수 있다. 전방주시 거리를 Ld라고 할 때 목표 곡률(Desired Curvature)은 일반적으로 kappa=2 sin(alpha)/Ld로 표현한다. 휠베이스(Wheelbase)가 L인 애커먼 차량에서는 등가 조향 명령(Equivalent Steering Command)을 delta=atan(L×kappa)로 계산할 수 있다.

전방주시 거리(Lookahead Distance)는 퓨어 퍼슈트의 동작 특성을 크게 결정한다. 짧은 전방주시 거리는 일반적으로 적극적인 보정(Aggressive Correction)을 발생시켜 국부적인 추종 오차를 줄일 수 있지만 조향 진동(Steering Oscillation)이나 위치추정 노이즈(Localization Noise)에 대한 민감도를 증가시킬 수 있다. 긴 전방주시 거리는 더욱 부드러운 조향과 향상된 안정성을 제공하지만 급격한 곡선에서 코너를 안쪽으로 가로지르거나 응답이 느려질 수 있다. 따라서 전방주시 거리 선택은 주요 보정 작업(Calibration Task) 중 하나이다.

적응형 전방주시(Adaptive Lookahead)는 서로 다른 운행 속도에서 성능을 향상시킨다. 일반적인 구현에서는 Ld=L0+Kv와 같이 기본값에 차량 속도에 비례하는 항을 추가하여 전방주시 거리를 정의한다. 저속 운전에서는 기동성을 위해 짧은 전방주시 거리를 사용하고 고속에서는 부드러운 응답을 위해 더 긴 전방주시 거리를 사용한다. 최소 및 최대 제한(Minimum and Maximum Limit)을 적용하여 비정상적인 전방주시 값이 생성되는 것을 방지한다.

이산 웨이포인트 경로(Discrete Waypoint Path)는 불규칙한 간격, 루프(Loop), 급격한 코너, 차량 뒤쪽의 경로점을 포함할 수 있으므로 목표점 선택(Target-Point Selection)은 신중하게 설계해야 한다. 제어기는 가장 가까운 유효 경로 영역을 식별하고 필요한 전방주시 거리에 도달할 때까지 경로 진행 방향을 따라 전방으로 검색해야 한다. 경로 샘플 사이를 보간(Interpolation)하면 전방주시 임계값을 초과하는 첫 번째 웨이포인트를 단순히 선택하는 것보다 부드러운 목표점을 얻을 수 있다.

스탠리 제어(Stanley Control)는 횡방향 경로 오차(Cross-Track Error)와 헤딩 오차(Heading Error)를 명시적으로 결합하여 경로 추종을 수행한다. 횡방향 경로 오차는 차량 기준점과 목표 경로 사이의 횡방향 거리를 의미하며, 헤딩 오차는 차량 방향과 경로 접선(Path Tangent) 사이의 각도 차이를 의미한다. 조향 명령은 두 오차를 동시에 보정하여 차량이 기준 경로로 직접 수렴하도록 한다.

일반적으로 사용되는 스탠리 관계식(Stanley Relationship)은 delta=psi_e+atan(k e_c/(v+epsilon))으로 표현할 수 있다. 여기서 psi_e는 헤딩 오차, e_c는 부호를 갖는 횡방향 경로 오차(Signed Cross-Track Error), k는 양의 제어 게인(Control Gain), v는 차량 속도, epsilon은 0에 가까운 속도에서 발생하는 수치 문제를 방지하기 위한 값이다. 헤딩 항은 차량 방향을 경로와 정렬시키고 횡방향 오차 항은 차량을 경로 방향으로 이동시킨다.

스탠리 게인(Stanley Gain)은 횡방향 변위가 조향에 얼마나 강하게 영향을 미치는지를 결정한다. 큰 게인은 강한 보정을 발생시키지만 특히 위치추정 노이즈나 액추에이터 지연(Actuator Delay)이 큰 경우 조향 진동을 유발할 수 있다. 작은 게인은 더 부드러운 동작을 제공하지만 더 큰 추종 오차를 허용할 수 있다. 따라서 게인 보정(Gain Calibration)은 속도 범위, 휠베이스, 조향 동역학(Steering Dynamics), 위치추정 품질, 경로 곡률, 제어 루프 주파수(Control-Loop Frequency)를 고려해야 한다.

스탠리 제어에서는 횡방향 오차 보정 항의 분모에 차량 속도가 포함되므로 저속 동작(Low-Speed Behavior)을 특별히 처리해야 한다. 보호 로직이 없으면 속도가 0에 가까워질수록 보정량이 과도하게 증가할 수 있다. 양의 완화 상수(Softening Constant), 최소 유효 속도(Minimum Effective Speed), 조향 포화(Steering Saturation), 속도 의존형 게인(Speed-Dependent Gain)을 사용하여 수치적 안정성을 유지할 수 있다. 후진 주행(Reverse Driving)에서는 헤딩, 경로 방향, 오차 부호를 일관되게 정의해야 한다.

횡방향 경로 오차는 단순한 절대 거리만이 아니라 부호를 갖는 값(Signed Value)으로 표현해야 한다. 부호는 차량이 기준 경로의 어느 쪽에 위치하는지를 나타내며 필요한 보정 방향을 결정한다. 일관된 좌표 규약(Coordinate Convention)을 통해 위치추정, 경로 표현(Path Representation), 제어기 계산, 조향 액추에이터 인터페이스 전체에서 양의 횡방향 오차, 양의 헤딩 오차, 양의 조향각을 명확하게 정의해야 한다.

따라서 퓨어 퍼슈트와 스탠리는 서로 다른 기하학적 정보를 사용한다. 퓨어 퍼슈트는 주로 전방 목표점의 기하학적 관계에 반응하며 자연스럽게 경로 예측(Path Preview)을 포함한다. 스탠리는 현재 횡방향 오차와 헤딩 오차를 직접 결합하여 기준 경로로 강하게 수렴할 수 있다. 어느 한 방법이 모든 상황에서 우월한 것은 아니며 실제 동작은 속도, 경로 형상, 차량 동역학, 위치추정 정확도, 파라미터 튜닝(Parameter Tuning)에 따라 달라진다.

경로 추종 소프트웨어는 알고리즘별 계산(Algorithm-Specific Calculation)을 공통 제어 인프라(Common Control Infrastructure)와 분리해야 한다. 경로 관리(Path Management), 좌표 변환, 위치추정 유효성 검증(Localization Validation), 명령 제약(Command Constraint), 액추에이터 인터페이스, 진단, 로깅(Logging)은 공통으로 유지하면서 퓨어 퍼슈트와 스탠리를 교체 가능한 제어기 모듈(Interchangeable Controller Module)로 구현할 수 있다. 이러한 구조는 제어된 비교를 지원하며 플랫폼이 운전 모드에 따라 알고리즘을 선택할 수 있도록 한다.

두 제어기에서 생성된 조향 명령은 액추에이터에 전달되기 전에 제한되어야 한다. 최대 조향각(Maximum Steering Angle), 곡률, 조향 변화율(Steering Rate), 조향 가속도(Steering Acceleration)는 차량 기하학과 액추에이터 성능을 준수해야 한다. 속도 의존형 제한(Speed-Dependent Limit)을 추가하면 고속에서 과도한 조향을 방지할 수 있다. 명령 필터링(Command Filtering)은 노이즈를 감소시킬 수 있지만 지나친 평활화(Smoothing)는 추종 지연을 발생시키므로 제어기 튜닝 및 액추에이터 대역폭과 함께 조정해야 한다.

경로 곡률(Path Curvature)은 피드포워드 요소(Feedforward Component)로도 사용할 수 있다. 기준 곡률이 알려져 있다면 제어기는 차량 운동학 모델(Vehicle Kinematic Model)에서 공칭 조향각(Nominal Steering Angle)을 생성하고 퓨어 퍼슈트 또는 스탠리 피드백을 이용하여 잔여 추종 오차(Residual Tracking Error)를 보정할 수 있다. 이러한 조합은 예측 가능한 곡선에서 필요한 피드백 동작량을 줄이고 경로 표현에 신뢰할 수 있는 곡률 정보가 포함된 경우 추종의 부드러움을 향상시킬 수 있다.

위치추정 품질(Localization Quality)은 경로 추종 성능을 직접 제한한다. 위치 점프(Position Jump), 헤딩 노이즈, 지연된 자세 추정(Delayed Pose Estimate), 일관되지 않은 타임스탬프는 제어 알고리즘이 수학적으로 정확하더라도 불필요한 조향 보정을 발생시킬 수 있다. 따라서 제어기는 자세 정보의 최신성과 유효성을 확인하고 측정값을 적절한 시간 기준(Time Reference)에 정렬하며 위치추정 신뢰도(Localization Confidence)가 부족해지는 경우 제한 없는 조향 명령을 생성하지 않아야 한다.

차량 속도와 조향 측정값 역시 제어 계산에 사용되는 자세 정보와 시간적으로 동기화되어야 한다. 고속에서는 중간 정도의 통신 또는 처리 지연만으로도 측정 시점과 실제 구동 시점 사이에서 차량 위치가 크게 이동할 수 있다. 타임스탬프 기반 동기화(Timestamp-Based Synchronization), 단기 자세 예측(Short-Horizon Pose Prediction), 지연 보상(Latency Compensation)을 사용하면 이러한 오차를 줄이고 제어기의 동작을 의도한 기하학적 모델과 더욱 일치시킬 수 있다.

경로 불연속(Path Discontinuity)은 명시적으로 처리해야 한다. 갑작스러운 웨이포인트 점프, 중복점(Duplicated Point), 지나치게 짧은 구간, 불연속적인 헤딩, 비현실적인 곡률은 큰 조향 명령을 발생시킬 수 있다. 경로 전처리(Path Preprocessing)는 중복점을 제거하고 일정한 간격으로 경로점을 재샘플링(Resampling)하며 형상을 평활화하고 접선 방향을 계산하며 곡률을 추정할 수 있다. 그러나 전처리만으로 런타임 입력의 유효성을 완전히 보장할 수 없으므로 추종 제어기 역시 입력 경로 데이터를 검증해야 한다.

제어기 상태 관리(Controller State Management)는 경로가 존재하지 않는 경우, 차량이 경로 끝에 도달한 경우, 위치추정이 유효하지 않은 경우, 속도가 제어 가능한 임계값 이하인 경우, 조향 시스템이 고장을 보고하는 경우의 동작을 정의해야 한다. 오래된 목표값을 계속 사용하는 대신 제어기는 정의된 유지(Hold), 정지(Stop), 또는 무효 출력 상태(Invalid-Output State)로 전환해야 한다. 하위 제어 계층으로 전달되는 조향 요구에는 명시적인 유효성 정보(Validity Information)가 함께 포함되어야 한다.

진단(Diagnostics)은 횡방향 경로 오차, 헤딩 오차, 선택된 목표점, 전방주시 거리, 요구 곡률, 조향 명령, 측정 조향각, 차량 속도, 위치추정 상태, 명령 포화(Command Saturation)를 모니터링해야 한다. 지속적으로 큰 오차가 발생하면 잘못된 튜닝, 액추에이터 한계, 위치추정 문제, 경로 결함(Path Defect), 과도한 속도 또는 예상하지 못한 타이어 미끄러짐(Tire Slip)을 나타낼 수 있다. 이러한 변수를 기록하면 현장 동작(Field Behavior)을 재현하고 체계적인 보정을 수행할 수 있다.

소프트웨어 인 더 루프 시험(Software-in-the-Loop Testing)은 직선 경로, 일정 반경 곡선(Constant-Radius Curve), S자 곡선(S-Curve), 급격한 전환, 희소 웨이포인트(Sparse Waypoint), 노이즈가 포함된 위치추정, 다양한 속도 조건에서 두 알고리즘을 평가해야 한다. 파라미터 스윕(Parameter Sweep)을 통해 전방주시 거리와 스탠리 게인에 대한 민감도를 정량화할 수 있다. 하드웨어 인 더 루프 시험(Hardware-in-the-Loop Testing)은 실제 차량에 제어기를 적용하기 전에 조향 액추에이터 지연, 조향각 포화, 통신 지연, 센서 노이즈를 추가하여 평가할 수 있다.

차량 시험(Vehicle Testing)은 로봇이 단순히 경로를 완주하는지만 평가해서는 안 된다. 유용한 성능 지표에는 평균 및 최대 횡방향 경로 오차, 헤딩 오차, 조향 변화율 활동도(Steering-Rate Activity), 명령 포화 발생 빈도(Command Saturation Frequency), 정착 동작(Settling Behavior), 오버슈트(Overshoot), 경로 완주 일관성(Path-Completion Consistency)이 포함된다. 지원되는 경우 전진과 후진, 저속 급선회, 고속 완만 곡선, 전환 구간, 실제 운용 환경을 대표하는 외란(Disturbance)을 포함하여 시험해야 한다.

따라서 강건한 경로 추종 아키텍처(Robust Path-Tracking Architecture)는 신뢰할 수 있는 경로 표현, 동기화된 위치추정, 좌표 변환, 기하학적 오차 계산(Geometric Error Calculation), 설정 가능한 퓨어 퍼슈트 또는 스탠리 제어, 조향 제약, 진단, 체계적인 검증을 통합해야 한다. 이러한 제어기는 계획된 궤적(Planned Trajectory)과 실제 조향 구동(Steering Actuation) 사이의 소프트웨어 연결부를 형성하여 애커먼 및 관련 이동 로봇 플랫폼이 예측 가능한 실시간 동작을 유지하면서 기준 경로를 정확하게 추종할 수 있도록 한다.

##  

## 04.06 Steering Safety: Torque Override / EStop [w/Code]

![](images/image6.png){width="7.268055555555556in" height="7.268055555555556in"}

Steering safety ensures that commanded wheel direction remains controllable and predictable during normal operation, degraded conditions, operator intervention, and system faults. Because unintended steering can immediately change vehicle trajectory, the steering subsystem requires independent supervision beyond nominal path-tracking or actuator control. Safety functions must detect abnormal commands, excessive torque, actuator faults, communication loss, and emergency requests within defined response times.

A steering safety architecture typically separates nominal control from safety supervision. The nominal controller calculates steering targets and actuator commands, while an independent safety layer evaluates command validity, measured angle, steering rate, torque, actuator status, communication health, and vehicle operating state. This separation reduces the possibility that one software failure can both generate an unsafe steering command and incorrectly classify the resulting behavior as acceptable.

Command validation forms the first protective boundary. Every steering request should be checked for numerical validity, permitted angle or curvature range, steering-rate limits, timestamp freshness, source authority, and consistency with the current operating mode. Commands containing invalid values, excessive discontinuities, expired timestamps, or unauthorized sources should be rejected before they propagate to the steering actuator or lower-level position controller.

Steering limits should reflect both mechanical capability and vehicle-level safety constraints. Maximum wheel angle, actuator travel, steering rate, and steering acceleration can be restricted according to vehicle speed, payload, operating mode, or surface condition. A command that is acceptable during low-speed maneuvering may produce excessive lateral acceleration at higher speed, so dynamic steering envelopes should become more restrictive as operating conditions demand.

Torque monitoring provides an additional safety channel because steering angle alone cannot reveal all abnormal conditions. Motor torque or current can indicate mechanical obstruction, steering linkage binding, collision forces, actuator saturation, or unexpected external intervention. The software should compare measured or estimated torque against operating-dependent thresholds and consider both magnitude and duration before declaring an overload or intervention condition.

Torque override allows an authorized external steering input to take precedence over automated steering under defined conditions. In a human-operated system, this may represent driver steering effort detected through a torque sensor. In robotic platforms, override may originate from a safety controller, remote operator, maintenance interface, or independent supervisory channel. The authority relationship must be explicitly defined so that competing commands cannot produce ambiguous actuator behavior.

Override detection should avoid responding to short noise spikes or normal steering disturbances. Thresholds, persistence timers, hysteresis, and filtered torque measurements can distinguish intentional intervention from transient loads. Once an override condition is confirmed, the transition from autonomous steering to the higher-priority source should be controlled to prevent abrupt angle changes, excessive steering rate, or unstable interaction between two simultaneously active controllers.

Command arbitration defines which steering source has control authority at any instant. Sources may include autonomous path tracking, manual control, remote operation, service functions, safety supervision, and emergency logic. A deterministic priority scheme should be associated with the steering state machine so that authority transitions are reproducible. Lower-priority commands may continue to be monitored but must not influence the actuator while a higher-priority source is active.

Emergency stop (E-Stop) handling requires careful coordination between propulsion, braking, and steering. An E-Stop should not automatically be interpreted as an instruction to instantaneously force the steering actuator to zero angle, because abrupt centering while the vehicle is moving can create an additional trajectory hazard. The required steering response depends on vehicle speed, mechanical design, braking behavior, operating environment, and the defined safe state.

A common E-Stop strategy removes propulsion torque, commands or enables braking, inhibits new autonomous motion commands, and places steering into a controlled safety state. Depending on the platform, steering may hold its current position, maintain controlled authority during deceleration, or return toward a predefined safe angle after vehicle speed has sufficiently decreased. The behavior should be explicitly specified rather than left as an unintended consequence of power removal.

Electrical power architecture strongly influences E-Stop behavior. If steering power is removed immediately, an electronically actuated steering system may lose the ability to maintain or safely change wheel direction. Some systems therefore retain safety-related steering power long enough to complete controlled deceleration or establish a safe wheel position. The software design must match the actual actuator, brake, power-distribution, and emergency circuit architecture.

E-Stop inputs should be treated as safety-critical signals with clearly defined detection and latching behavior. Physical emergency buttons, safety PLCs, remote safety devices, collision systems, or supervisory controllers may generate stop requests. Software should identify the active source, validate signal state where applicable, latch the emergency condition when required, and prevent normal motion from resuming merely because a transient stop signal disappears.

Reset behavior is as important as E-Stop activation. Releasing an emergency button should not automatically restore steering authority or vehicle motion. The system should require defined reset conditions such as valid sensors, healthy communication, acceptable actuator state, confirmed steering position, cleared critical faults, and explicit restart authorization. This prevents uncontrolled recovery when the original cause of the emergency condition has not been resolved.

The steering state machine can coordinate states such as initialization, standby, normal control, override, degraded operation, emergency stop, fault-latched state, and recovery. Each state should define permitted command sources, actuator behavior, steering limits, diagnostic requirements, and transition conditions. Explicit state logic makes safety behavior deterministic and prevents individual software modules from independently interpreting emergency or override conditions.

Tracking-error supervision compares commanded and measured steering positions. Excessive error may indicate actuator failure, mechanical blockage, sensor error, insufficient torque, or communication problems. Thresholds should consider steering velocity and actuator dynamics because temporary error during rapid motion can be normal. Persistent or unexpectedly growing error should trigger degradation or a safety response before the steering state becomes uncontrollable.

Steering-rate supervision detects unintended rapid wheel movement even when the measured angle remains inside its permitted range. The safety monitor can calculate steering rate from independent position measurements and compare it with commanded motion and physical actuator limits. Unexpected motion may indicate controller instability, corrupted commands, sensor faults, or actuator electronics failure and should therefore be treated independently from simple angle-limit monitoring.

Redundant sensing and computation can improve fault tolerance when required by the safety concept. Independent steering-angle sensors, motor position feedback, torque sensing, separate processors, watchdogs, or safety communication channels can cross-check nominal control behavior. Redundancy should include disagreement detection and defined fault responses; duplicating components without a strategy for determining which information remains trustworthy provides limited safety benefit.

Communication supervision is essential when steering commands or safety states are transmitted over CAN, CAN FD, Automotive Ethernet, EtherCAT, or similar networks. Sequence counters, timestamps, timeouts, message integrity checks, and source identification help detect stale, repeated, corrupted, or missing information. Safety-related communication faults should transition the steering subsystem to a predefined degraded or safe behavior within the required fault-tolerant time.

Watchdog supervision detects failures in software execution and controller timing. A steering controller that stops updating, executes too slowly, or becomes trapped in an invalid state can be as hazardous as an incorrect steering command. Independent watchdog mechanisms should therefore monitor task execution, communication activity, control-cycle timing, and critical state transitions, with defined reactions when timing requirements are violated.

Degraded steering operation can preserve limited mobility when a fault does not require immediate complete shutdown. Examples include reducing maximum speed, limiting steering angle or rate, disabling rear steering in a 4WS platform, or switching from autonomous control to a validated backup channel. Degraded operation should be explicitly authorized by the safety concept and should not be used to conceal faults whose consequences are insufficiently understood.

A safe-stop strategy should consider the combined vehicle motion rather than steering in isolation. Steering, drive torque, braking, vehicle speed, path curvature, terrain, and surrounding obstacles influence the safest achievable stop trajectory. The steering subsystem should therefore exchange status and safety requests with the vehicle motion supervisor so that deceleration and steering actions are coordinated instead of being independently commanded by unrelated controllers.

Diagnostics should record the sequence of events surrounding an override, E-Stop, or steering fault. Useful information includes command source, requested and measured angle, steering rate, motor current or torque, vehicle speed, safety state, communication status, fault flags, override thresholds, and timestamps. Pre-trigger and post-trigger buffering is particularly valuable for determining whether steering abnormality caused an emergency event or occurred as a consequence of it.

Verification should include nominal steering, command saturation, torque threshold crossing, intentional override, communication timeout, sensor disagreement, actuator blockage, excessive tracking error, unexpected steering motion, watchdog expiration, E-Stop activation, and reset sequences. Software-in-the-loop and hardware-in-the-loop environments allow these faults to be injected repeatedly before equivalent scenarios are evaluated on the physical vehicle.

Vehicle testing should verify that safety transitions produce predictable motion rather than merely confirming that software states change correctly. Tests should examine different steering angles and vehicle speeds when an override or E-Stop occurs, including straight travel and curved paths. Stopping distance, trajectory deviation, steering-rate response, actuator behavior, and recovery conditions should be measured against predefined acceptance criteria.

A robust steering safety design therefore combines command validation, dynamic limits, torque supervision, deterministic override arbitration, coordinated E-Stop behavior, redundant monitoring, communication supervision, state management, diagnostics, and systematic fault testing. By integrating steering safety with propulsion and braking supervision, an autonomous vehicle or mobile robot can transition predictably from normal control to degraded operation or a controlled safe stop when abnormal conditions occur.

조향 안전(Steering Safety)은 정상 운전(Normal Operation), 성능 저하 조건(Degraded Condition), 운전자 개입(Operator Intervention), 시스템 고장(System Fault) 상황에서도 명령된 바퀴 방향이 제어 가능하고 예측 가능한 상태를 유지하도록 한다. 의도하지 않은 조향(Unintended Steering)은 차량 궤적을 즉시 변화시킬 수 있으므로 조향 서브시스템(Steering Subsystem)은 일반적인 경로 추종(Path Tracking) 또는 액추에이터 제어(Actuator Control)와 독립된 감독 기능을 필요로 한다. 안전 기능은 비정상 명령, 과도한 토크, 액추에이터 고장, 통신 손실, 비상 요청을 정의된 응답 시간 내에 탐지해야 한다.

조향 안전 아키텍처(Steering Safety Architecture)는 일반적으로 정상 제어(Nominal Control)와 안전 감독(Safety Supervision)을 분리한다. 정상 제어기는 조향 목표와 액추에이터 명령을 계산하고 독립적인 안전 계층(Independent Safety Layer)은 명령 유효성, 측정 조향각, 조향 변화율(Steering Rate), 토크, 액추에이터 상태, 통신 건전성(Communication Health), 차량 운전 상태를 평가한다. 이러한 분리는 하나의 소프트웨어 고장이 위험한 조향 명령을 생성하는 동시에 그 결과를 정상적인 동작으로 잘못 판단할 가능성을 줄인다.

명령 검증(Command Validation)은 첫 번째 보호 경계(Protective Boundary)를 형성한다. 모든 조향 요구는 수치적 유효성(Numerical Validity), 허용된 조향각 또는 곡률 범위, 조향 변화율 제한, 타임스탬프 최신성(Timestamp Freshness), 명령원 권한(Source Authority), 현재 운전 모드와의 일관성을 검사해야 한다. 유효하지 않은 값, 과도한 불연속, 만료된 타임스탬프 또는 권한이 없는 명령원이 포함된 명령은 조향 액추에이터나 하위 위치 제어기로 전달되기 전에 거부해야 한다.

조향 제한(Steering Limit)은 기계적 성능뿐만 아니라 차량 수준 안전 제약(Vehicle-Level Safety Constraint)도 반영해야 한다. 최대 바퀴 조향각(Maximum Wheel Angle), 액추에이터 이동 범위(Actuator Travel), 조향 변화율, 조향 가속도(Steering Acceleration)는 차량 속도, 페이로드(Payload), 운전 모드 또는 노면 상태(Surface Condition)에 따라 제한할 수 있다. 저속 기동에서는 허용 가능한 명령이라도 고속에서는 과도한 횡가속도(Lateral Acceleration)를 발생시킬 수 있으므로 운전 조건에 따라 동적 조향 영역(Dynamic Steering Envelope)을 더욱 제한해야 한다.

토크 모니터링(Torque Monitoring)은 조향각만으로 모든 비정상 상태를 확인할 수 없기 때문에 추가적인 안전 채널(Safety Channel)을 제공한다. 모터 토크 또는 전류는 기계적 장애물(Mechanical Obstruction), 조향 링크 구속(Steering Linkage Binding), 충돌력(Collision Force), 액추에이터 포화(Actuator Saturation), 예상하지 못한 외부 개입(External Intervention)을 나타낼 수 있다. 소프트웨어는 측정 또는 추정된 토크를 운전 조건에 따른 임계값과 비교하고 과부하 또는 개입 상태를 판단하기 전에 크기와 지속 시간을 함께 고려해야 한다.

토크 오버라이드(Torque Override)는 정의된 조건에서 승인된 외부 조향 입력(Authorized External Steering Input)이 자동 조향보다 높은 우선순위를 갖도록 한다. 사람이 운전하는 시스템에서는 토크 센서를 통해 감지되는 운전자의 조향력이 이에 해당할 수 있다. 로봇 플랫폼에서는 안전 제어기(Safety Controller), 원격 운전자(Remote Operator), 유지보수 인터페이스(Maintenance Interface), 독립적인 감독 채널(Independent Supervisory Channel)에서 오버라이드가 발생할 수 있다. 서로 경쟁하는 명령이 모호한 액추에이터 동작을 발생시키지 않도록 권한 관계(Authority Relationship)를 명확하게 정의해야 한다.

오버라이드 검출(Override Detection)은 짧은 노이즈 스파이크(Noise Spike)나 정상적인 조향 외란(Steering Disturbance)에 반응하지 않아야 한다. 임계값, 지속 시간 타이머(Persistence Timer), 히스테리시스(Hysteresis), 필터링된 토크 측정값을 사용하여 의도적인 개입과 일시적인 부하를 구분할 수 있다. 오버라이드 조건이 확인되면 자율 조향에서 상위 우선순위 명령원으로의 전환을 제어하여 급격한 조향각 변화, 과도한 조향 변화율 또는 동시에 활성화된 두 제어기 사이의 불안정한 상호작용을 방지해야 한다.

명령 중재(Command Arbitration)는 특정 시점에 어떤 조향 명령원이 제어 권한(Control Authority)을 갖는지를 정의한다. 명령원에는 자율 경로 추종(Autonomous Path Tracking), 수동 제어(Manual Control), 원격 운전(Remote Operation), 서비스 기능(Service Function), 안전 감독, 비상 로직(Emergency Logic)이 포함될 수 있다. 결정론적 우선순위 체계(Deterministic Priority Scheme)는 조향 상태 머신(Steering State Machine)과 연계되어 권한 전환을 재현 가능하게 만들어야 한다. 상위 우선순위 명령원이 활성화된 동안 하위 우선순위 명령은 계속 모니터링할 수 있지만 액추에이터에는 영향을 주어서는 안 된다.

비상 정지(Emergency Stop, E-Stop) 처리는 추진(Propulsion), 제동(Braking), 조향 사이의 세심한 협조가 필요하다. E-Stop을 조향 액추에이터를 즉시 0도 위치로 강제하는 명령으로 자동 해석해서는 안 된다. 차량이 움직이는 동안 갑작스럽게 조향을 중앙으로 복귀시키면 추가적인 궤적 위험(Trajectory Hazard)이 발생할 수 있기 때문이다. 필요한 조향 대응은 차량 속도, 기계적 설계, 제동 동작, 운용 환경, 정의된 안전 상태(Safe State)에 따라 달라진다.

일반적인 E-Stop 전략(E-Stop Strategy)은 추진 토크를 제거하고, 제동을 명령하거나 활성화하며, 새로운 자율 운동 명령을 차단하고, 조향을 제어된 안전 상태(Controlled Safety State)로 전환한다. 플랫폼에 따라 조향은 현재 위치를 유지하거나 감속 중 제어 권한을 유지할 수 있으며, 차량 속도가 충분히 감소한 후 사전에 정의된 안전 조향각(Safe Steering Angle) 방향으로 복귀할 수 있다. 이러한 동작은 단순한 전원 차단의 의도하지 않은 결과로 남겨두지 않고 명시적으로 규정해야 한다.

전기 전원 아키텍처(Electrical Power Architecture)는 E-Stop 동작에 큰 영향을 준다. 조향 전원을 즉시 차단하면 전자식 조향 시스템(Electronically Actuated Steering System)이 바퀴 방향을 유지하거나 안전하게 변경하는 능력을 잃을 수 있다. 따라서 일부 시스템에서는 제어된 감속을 완료하거나 바퀴를 안전 위치로 설정할 수 있을 때까지 안전 관련 조향 전원(Safety-Related Steering Power)을 일정 시간 유지한다. 소프트웨어 설계는 실제 액추에이터, 브레이크, 전력 분배(Power Distribution), 비상 회로(Emergency Circuit) 아키텍처와 일치해야 한다.

E-Stop 입력은 명확하게 정의된 검출 및 래칭 동작(Latching Behavior)을 갖는 안전 중요 신호(Safety-Critical Signal)로 처리해야 한다. 물리적 비상 정지 버튼, 안전 PLC(Safety PLC), 원격 안전 장치(Remote Safety Device), 충돌 시스템(Collision System), 감독 제어기(Supervisory Controller)가 정지 요청을 생성할 수 있다. 소프트웨어는 활성화된 명령원을 식별하고 필요한 경우 신호 상태를 검증하며, 요구되는 경우 비상 상태를 래치하고, 일시적인 정지 신호가 사라졌다는 이유만으로 정상 운동이 다시 시작되지 않도록 해야 한다.

리셋 동작(Reset Behavior)은 E-Stop 활성화만큼 중요하다. 비상 정지 버튼을 해제했다고 해서 조향 권한이나 차량 운동이 자동으로 복구되어서는 안 된다. 시스템은 유효한 센서, 정상적인 통신, 허용 가능한 액추에이터 상태, 확인된 조향 위치, 해제된 중요 고장(Critical Fault), 명시적인 재시작 승인(Restart Authorization) 등의 정의된 리셋 조건을 요구해야 한다. 이를 통해 비상 상황의 원래 원인이 해결되지 않은 상태에서 제어되지 않은 복구가 이루어지는 것을 방지한다.

조향 상태 머신은 초기화(Initialization), 대기(Standby), 정상 제어(Normal Control), 오버라이드(Override), 성능 저하 운전(Degraded Operation), 비상 정지, 고장 래치 상태(Fault-Latched State), 복구(Recovery) 등의 상태를 조정할 수 있다. 각 상태에서는 허용되는 명령원, 액추에이터 동작, 조향 제한, 진단 요구사항, 전환 조건을 정의해야 한다. 명시적인 상태 로직(State Logic)은 안전 동작을 결정론적으로 만들고 개별 소프트웨어 모듈이 비상 또는 오버라이드 조건을 서로 다르게 해석하는 것을 방지한다.

추종 오차 감독(Tracking-Error Supervision)은 명령된 조향 위치와 측정된 조향 위치를 비교한다. 과도한 오차는 액추에이터 고장, 기계적 구속(Mechanical Blockage), 센서 오류, 부족한 토크, 통신 문제를 나타낼 수 있다. 급격한 조향 동작 중에는 일시적인 오차가 정상일 수 있으므로 임계값은 조향 속도와 액추에이터 동역학(Actuator Dynamics)을 고려해야 한다. 지속되거나 예상과 다르게 증가하는 오차는 조향 상태가 제어 불가능해지기 전에 성능 저하 또는 안전 대응을 발생시켜야 한다.

조향 변화율 감독(Steering-Rate Supervision)은 측정 조향각이 허용 범위 내에 있더라도 의도하지 않은 빠른 바퀴 움직임을 탐지한다. 안전 모니터는 독립적인 위치 측정값으로부터 조향 변화율을 계산하고 이를 명령된 움직임 및 물리적 액추에이터 한계와 비교할 수 있다. 예상하지 못한 움직임은 제어기 불안정(Controller Instability), 손상된 명령(Corrupted Command), 센서 고장, 액추에이터 전자회로 고장(Actuator Electronics Failure)을 나타낼 수 있으므로 단순한 조향각 한계 모니터링과 독립적으로 처리해야 한다.

안전 개념(Safety Concept)에서 요구되는 경우 이중화 센싱 및 연산(Redundant Sensing and Computation)을 통해 고장 허용성(Fault Tolerance)을 향상시킬 수 있다. 독립적인 조향각 센서, 모터 위치 피드백, 토크 센싱, 별도의 프로세서(Separate Processor), 워치독(Watchdog), 안전 통신 채널(Safety Communication Channel)을 이용하여 정상 제어 동작을 교차 검증할 수 있다. 이중화에는 불일치 검출(Disagreement Detection)과 정의된 고장 대응이 포함되어야 하며, 어떤 정보가 여전히 신뢰할 수 있는지를 판단하는 전략 없이 단순히 구성요소를 복제하는 것은 제한적인 안전 효과만 제공한다.

조향 명령 또는 안전 상태가 CAN, CAN FD, 자동차 이더넷(Automotive Ethernet), EtherCAT 또는 유사한 네트워크를 통해 전달되는 경우 통신 감독(Communication Supervision)이 필수적이다. 시퀀스 카운터(Sequence Counter), 타임스탬프, 타임아웃, 메시지 무결성 검사(Message Integrity Check), 명령원 식별(Source Identification)을 이용하여 오래된 정보, 반복된 정보, 손상된 정보 또는 누락된 정보를 탐지할 수 있다. 안전 관련 통신 고장은 요구되는 고장 허용 시간(Fault-Tolerant Time) 내에 조향 서브시스템을 사전에 정의된 성능 저하 또는 안전 동작으로 전환시켜야 한다.

워치독 감독(Watchdog Supervision)은 소프트웨어 실행과 제어기 타이밍의 고장을 탐지한다. 갱신이 중단되거나 지나치게 느리게 실행되거나 유효하지 않은 상태에 갇힌 조향 제어기는 잘못된 조향 명령만큼 위험할 수 있다. 따라서 독립적인 워치독 메커니즘(Independent Watchdog Mechanism)은 태스크 실행(Task Execution), 통신 활동, 제어 주기 타이밍(Control-Cycle Timing), 중요 상태 전환을 모니터링하고 타이밍 요구사항이 위반될 경우 정의된 대응을 수행해야 한다.

성능 저하 조향 운전(Degraded Steering Operation)은 고장이 즉각적인 완전 정지를 요구하지 않는 경우 제한적인 이동 능력을 유지할 수 있다. 예를 들어 최대 속도를 낮추거나 조향각 또는 조향 변화율을 제한하고, 4륜 조향(4WS) 플랫폼에서 후륜 조향을 비활성화하거나 자율 제어에서 검증된 백업 채널(Validated Backup Channel)로 전환할 수 있다. 성능 저하 운전은 안전 개념에 의해 명시적으로 허용되어야 하며 결과가 충분히 이해되지 않은 고장을 감추기 위한 수단으로 사용해서는 안 된다.

안전 정지 전략(Safe-Stop Strategy)은 조향만 독립적으로 고려하지 않고 통합된 차량 운동(Combined Vehicle Motion)을 고려해야 한다. 조향, 구동 토크(Drive Torque), 제동, 차량 속도, 경로 곡률(Path Curvature), 지형(Terrain), 주변 장애물은 구현 가능한 가장 안전한 정지 궤적에 영향을 준다. 따라서 조향 서브시스템은 차량 운동 감독기(Vehicle Motion Supervisor)와 상태 및 안전 요청을 교환하여 서로 관련 없는 제어기가 감속과 조향 동작을 독립적으로 명령하지 않고 상호 협조하도록 해야 한다.

진단(Diagnostics)은 오버라이드, E-Stop 또는 조향 고장 전후의 이벤트 순서를 기록해야 한다. 유용한 정보에는 명령원, 요구 및 측정 조향각, 조향 변화율, 모터 전류 또는 토크, 차량 속도, 안전 상태, 통신 상태, 고장 플래그(Fault Flag), 오버라이드 임계값(Override Threshold), 타임스탬프가 포함된다. 사전 트리거 및 사후 트리거 버퍼링(Pre-Trigger and Post-Trigger Buffering)은 조향 이상이 비상 이벤트의 원인이었는지 또는 그 결과로 발생했는지를 판단하는 데 특히 유용하다.

검증(Verification)은 정상 조향, 명령 포화(Command Saturation), 토크 임계값 초과, 의도적인 오버라이드, 통신 타임아웃, 센서 불일치, 액추에이터 구속(Actuator Blockage), 과도한 추종 오차, 예상하지 못한 조향 움직임, 워치독 만료(Watchdog Expiration), E-Stop 활성화, 리셋 시퀀스(Reset Sequence)를 포함해야 한다. 소프트웨어 인 더 루프(Software-in-the-Loop)와 하드웨어 인 더 루프(Hardware-in-the-Loop) 환경에서는 실제 차량에서 동일한 시나리오를 평가하기 전에 이러한 고장을 반복적으로 주입하여 검증할 수 있다.

차량 시험(Vehicle Testing)은 단순히 소프트웨어 상태가 올바르게 변경되는지만 확인하지 않고 안전 상태 전환이 예측 가능한 차량 운동을 생성하는지 검증해야 한다. 시험에서는 직선 주행과 곡선 경로를 포함하여 서로 다른 조향각 및 차량 속도에서 오버라이드 또는 E-Stop이 발생하는 상황을 평가해야 한다. 정지 거리(Stopping Distance), 궤적 편차(Trajectory Deviation), 조향 변화율 응답, 액추에이터 동작, 복구 조건을 사전에 정의된 합격 기준(Acceptance Criteria)과 비교하여 측정해야 한다.

따라서 강건한 조향 안전 설계(Robust Steering Safety Design)는 명령 검증, 동적 제한(Dynamic Limit), 토크 감독(Torque Supervision), 결정론적 오버라이드 중재(Deterministic Override Arbitration), 협조된 E-Stop 동작(Coordinated E-Stop Behavior), 이중화 모니터링(Redundant Monitoring), 통신 감독, 상태 관리(State Management), 진단, 체계적인 고장 시험(Systematic Fault Testing)을 통합해야 한다. 조향 안전을 추진 및 제동 감독과 통합함으로써 자율주행 차량 또는 이동 로봇은 비정상적인 조건이 발생했을 때 정상 제어에서 성능 저하 운전 또는 제어된 안전 정지(Controlled Safe Stop) 상태로 예측 가능하게 전환할 수 있다.

##  

## 04.07 Steering Friction Compensation and Hysteresis [w/Code]

![](images/image7.png){width="7.268055555555556in" height="7.268055555555556in"}

Steering friction and hysteresis are nonlinear effects that prevent the commanded actuator position or torque from producing an immediately proportional wheel-angle response. Friction originates from bearings, seals, gears, rack mechanisms, joints, tires, and actuator components, while hysteresis arises from backlash, elastic deformation, compliance, and direction-dependent mechanical behavior. These effects can degrade steering accuracy, especially during small corrections and direction reversals.

In an ideal steering model, a commanded actuator displacement produces a repeatable steering angle independent of motion history. Real mechanisms behave differently because static friction must be overcome before movement begins and mechanical clearances may be taken up before wheel motion appears. Consequently, identical commands can generate different measured steering angles depending on whether the mechanism approaches the target from the left or right direction.

Static friction, often called stiction, is particularly important near zero steering velocity. When actuator effort remains below the breakaway threshold, the steering mechanism may remain stationary even though the controller continues increasing its command. Once the threshold is exceeded, the mechanism can suddenly move, creating stick-slip behavior. This phenomenon can cause oscillation around small steering targets and reduce low-speed path-tracking precision.

Coulomb friction represents an approximately constant resisting force or torque acting opposite the direction of motion. Viscous friction increases with steering velocity and can often be represented by a velocity-proportional term. Practical steering mechanisms may exhibit combinations of static, Coulomb, viscous, and Stribeck-like friction, making a single linear damping coefficient insufficient for accurate compensation across the complete operating range.

Hysteresis describes the dependence of steering output on previous mechanical state and direction of approach. When steering direction reverses, actuator motion may initially remove gear clearance, rack backlash, joint compliance, or structural deformation before producing a corresponding wheel-angle change. The resulting input-output relationship forms a loop rather than a single-valued curve, meaning that steering angle cannot always be predicted from actuator position alone.

Friction and hysteresis can be characterized experimentally by applying slow steering sweeps across the operating range while recording command position, measured actuator position, wheel angle, motor current, torque, and direction of motion. Repeating sweeps in both directions reveals asymmetric behavior, breakaway forces, dead zones, and hysteresis width. Multiple repetitions help distinguish systematic nonlinear characteristics from sensor noise or temporary disturbances.

Steering calibration should therefore consider direction-dependent behavior rather than relying only on a single position-to-angle mapping. Separate left-to-right and right-to-left calibration curves can represent mechanical hysteresis more accurately. The software can select or interpolate between these maps according to motion direction and recent steering history, while maintaining continuous transitions when the steering velocity passes through zero.

A simple friction compensation strategy adds feedforward actuator effort based on the requested direction of motion. When positive steering motion is requested, a calibrated positive compensation torque is added, while negative motion receives compensation of the opposite sign. This offsets a portion of Coulomb friction before feedback error accumulates, improving response without requiring the position controller to generate the entire breakaway effort.

Static-friction compensation requires additional care because a discontinuous sign-based command near zero velocity can itself cause chatter. A deadband, smooth sign approximation, velocity threshold, or state-dependent compensation function can provide a gradual transition around zero. Compensation should be limited so that sensor noise or very small target changes do not repeatedly trigger alternating breakaway torque and unnecessary steering movement.

Velocity-dependent compensation can represent viscous and Stribeck-like behavior more accurately. The compensation model may combine a constant directional term with functions of measured or requested steering velocity. At very low speed, compensation can approach the identified breakaway requirement, while at higher speed the model can transition toward Coulomb and viscous terms. Parameters should be identified from actual actuator and steering-system measurements.

Position deadband compensation addresses mechanical regions where small actuator movements do not immediately change wheel angle. When direction reverses, the controller can account for a calibrated backlash distance before expecting proportional wheel response. However, deadband compensation should not simply command an abrupt position jump, because this can produce excessive motion once mechanical clearance is removed. Rate-limited compensation provides safer behavior.

Hysteresis compensation can also use stateful models that explicitly represent motion history. Instead of treating steering conversion as a memoryless function, the software maintains information about previous direction, reversal point, accumulated backlash, or elastic state. More advanced implementations may use identified hysteresis models, but their added complexity should be justified by measurable improvement in steering accuracy and robustness.

Closed-loop feedback remains necessary even when feedforward compensation is applied. Friction changes with temperature, wear, lubrication, payload, tire contact forces, road surface, and component aging, so a fixed compensation map cannot perfectly represent every condition. Feedforward should reduce predictable nonlinear effects, while the feedback controller corrects remaining errors and maintains accuracy when operating conditions differ from calibration conditions.

Integral control can overcome steady friction-induced error, but excessive integral action may create windup while the mechanism is stuck. The controller can accumulate a large command during stiction and then produce overshoot when movement suddenly begins. Anti-windup logic, conditional integration, integral limits, or friction-aware integrator activation can reduce this behavior and improve settling around small steering-angle targets.

Derivative or velocity feedback can improve damping after breakaway, but steering-rate estimates are sensitive to measurement noise. Filter design must balance noise rejection against control delay. Because friction compensation often depends on the sign and magnitude of steering velocity, noisy rate estimates can cause compensation switching. Hysteresis, filtering, and minimum-velocity thresholds can stabilize direction detection near zero speed.

Direction reversal deserves explicit software handling because it is where backlash and hysteresis become most visible. The controller can detect a reversal when requested or measured steering velocity changes sign and then enter a temporary compensation state. During this phase, the software may apply calibrated preload or backlash compensation while monitoring actual wheel response before returning to normal position tracking.

Compensation limits are necessary because model errors must not generate unsafe steering commands. Maximum additional torque, current, position offset, or feedforward contribution should be bounded independently from the nominal controller output. The combined command must still satisfy actuator and vehicle-level steering limits. Safety supervision should evaluate the final commanded motion rather than assuming that compensation terms are inherently safe.

Temperature monitoring can improve compensation because lubricant viscosity, seal friction, motor characteristics, and mechanical clearances can change significantly between cold startup and warmed operation. Calibration may include temperature-dependent friction parameters or separate operating regions. Any temperature compensation should remain bounded and should revert to a defined conservative behavior when temperature measurements are unavailable or invalid.

Mechanical wear can gradually change backlash and friction characteristics over the service life of the steering system. Diagnostic software can monitor long-term trends in breakaway current, position error during reversal, hysteresis width, or compensation demand. Increasing values may indicate lubrication degradation, gear wear, joint looseness, alignment problems, or actuator deterioration and can support predictive maintenance before tracking performance becomes unacceptable.

Adaptive compensation may update selected parameters during operation when sufficient observability exists. For example, repeated low-speed steering reversals can provide information about current backlash or breakaway torque. Adaptation should occur slowly, remain within validated bounds, and preserve baseline calibration values. A genuine mechanical failure must not be absorbed indefinitely into an adaptive model and incorrectly treated as normal aging.

The processed steering state should expose information relevant to friction compensation and diagnostics. Useful signals include requested angle, measured angle, position error, steering velocity, motion direction, reversal state, feedforward compensation, motor current, estimated torque, temperature, and saturation state. These signals help higher-level diagnostics distinguish ordinary tracking error from nonlinear mechanical behavior.

Software verification should test zero-speed behavior, small steering corrections, slow sweeps, direction reversals, large-angle motion, compensation saturation, temperature-dependent parameters, sensor noise, and actuator delay. Simulation models should include static friction, Coulomb friction, viscous effects, backlash, and hysteresis so that compensation algorithms can be evaluated systematically before physical vehicle testing.

Hardware-in-the-loop testing can reproduce actuator loading, sensor quantization, communication timing, and controller execution while injecting known friction or backlash characteristics. Physical bench testing should then measure breakaway torque, steady steering effort, reversal response, and repeatability. Comparing compensated and uncompensated results provides objective evidence that the algorithm improves tracking without creating oscillation or excessive actuator stress.

Vehicle-level validation should evaluate the effect of compensation on actual path-tracking performance. Straight-line corrections, low-speed curves, parking maneuvers, repeated S-turns, and small steering reversals are useful scenarios because friction and hysteresis are most visible when steering commands repeatedly cross small-error regions. Metrics can include cross-track error, steering error, oscillation, settling time, and actuator current.

A robust steering friction and hysteresis compensation design therefore combines mechanical characterization, direction-dependent calibration, feedforward friction compensation, backlash handling, closed-loop feedback, anti-windup protection, temperature awareness, bounded adaptation, diagnostics, and systematic validation. By compensating predictable nonlinearities without hiding mechanical faults, the steering system can achieve smoother and more repeatable control throughout its operating life.

조향 마찰(Steering Friction)과 히스테리시스(Hysteresis)는 명령된 액추에이터 위치 또는 토크가 즉각적으로 비례하는 바퀴 조향각 응답을 생성하지 못하게 하는 비선형 효과(Nonlinear Effect)이다. 마찰은 베어링(Bearing), 씰(Seal), 기어(Gear), 랙 기구(Rack Mechanism), 조인트(Joint), 타이어(Tire), 액추에이터 구성요소에서 발생하며, 히스테리시스는 백래시(Backlash), 탄성 변형(Elastic Deformation), 컴플라이언스(Compliance), 방향 의존적 기계 동작(Direction-Dependent Mechanical Behavior)에서 발생한다. 이러한 효과는 특히 작은 조향 보정과 방향 전환 과정에서 조향 정확도를 저하시킬 수 있다.

이상적인 조향 모델(Ideal Steering Model)에서는 명령된 액추에이터 변위가 이전 운동 이력(Motion History)에 관계없이 반복 가능한 조향각을 생성한다. 실제 기구에서는 움직임이 시작되기 전에 정지 마찰(Static Friction)을 극복해야 하며, 바퀴 움직임이 나타나기 전에 기계적 유격(Mechanical Clearance)이 먼저 제거될 수 있기 때문에 다르게 동작한다. 따라서 동일한 명령이라도 조향 기구가 목표값에 좌측 방향에서 접근하는지 우측 방향에서 접근하는지에 따라 서로 다른 측정 조향각을 생성할 수 있다.

스틱션(Stiction)이라고도 하는 정지 마찰(Static Friction)은 조향 속도가 0에 가까운 영역에서 특히 중요하다. 액추에이터 구동력이 이탈 임계값(Breakaway Threshold)보다 낮게 유지되면 제어기가 명령을 계속 증가시키더라도 조향 기구는 정지 상태를 유지할 수 있다. 임계값을 초과하면 기구가 갑자기 움직이면서 스틱-슬립 동작(Stick-Slip Behavior)을 발생시킬 수 있다. 이러한 현상은 작은 조향 목표 주변에서 진동을 발생시키고 저속 경로 추종(Path Tracking)의 정밀도를 저하시킬 수 있다.

쿨롱 마찰(Coulomb Friction)은 운동 방향과 반대 방향으로 작용하는 거의 일정한 저항력 또는 토크를 나타낸다. 점성 마찰(Viscous Friction)은 조향 속도에 따라 증가하며 일반적으로 속도에 비례하는 항으로 표현할 수 있다. 실제 조향 기구에서는 정지 마찰, 쿨롱 마찰, 점성 마찰, 스트리벡 형태 마찰(Stribeck-Like Friction)이 복합적으로 나타날 수 있으므로 하나의 선형 감쇠 계수(Linear Damping Coefficient)만으로 전체 운전 영역에서 정확한 보상을 수행하기 어렵다.

히스테리시스는 조향 출력이 이전의 기계적 상태(Previous Mechanical State)와 접근 방향(Direction of Approach)에 의존하는 현상을 의미한다. 조향 방향이 반전되면 액추에이터 운동이 바퀴 조향각 변화로 나타나기 전에 기어 유격(Gear Clearance), 랙 백래시(Rack Backlash), 조인트 컴플라이언스(Joint Compliance), 구조적 변형(Structural Deformation)을 먼저 제거해야 할 수 있다. 이로 인해 입출력 관계(Input-Output Relationship)는 하나의 단일값 곡선이 아니라 루프(Loop)를 형성하며, 액추에이터 위치만으로 조향각을 항상 정확하게 예측할 수 없게 된다.

마찰과 히스테리시스는 운전 범위 전체에서 느린 조향 스윕(Slow Steering Sweep)을 수행하면서 명령 위치, 측정 액추에이터 위치, 바퀴 조향각, 모터 전류, 토크, 운동 방향을 기록하여 실험적으로 특성화(Characterization)할 수 있다. 양방향으로 반복적인 스윕을 수행하면 비대칭 동작(Asymmetric Behavior), 이탈력(Breakaway Force), 데드존(Dead Zone), 히스테리시스 폭(Hysteresis Width)을 확인할 수 있다. 여러 번 반복하면 체계적인 비선형 특성과 센서 노이즈 또는 일시적인 외란을 구분하는 데 도움이 된다.

따라서 조향 보정(Steering Calibration)은 하나의 위치-각도 매핑(Position-to-Angle Mapping)에만 의존하기보다 방향 의존적인 동작(Direction-Dependent Behavior)을 고려해야 한다. 좌측에서 우측(Left-to-Right) 및 우측에서 좌측(Right-to-Left)으로 이동할 때 서로 다른 보정 곡선(Calibration Curve)을 사용하면 기계적 히스테리시스를 더욱 정확하게 표현할 수 있다. 소프트웨어는 운동 방향과 최근 조향 이력에 따라 이러한 맵을 선택하거나 보간하면서 조향 속도가 0을 통과할 때 연속적인 전환을 유지할 수 있다.

단순한 마찰 보상 전략(Friction Compensation Strategy)은 요구된 운동 방향에 따라 피드포워드 액추에이터 구동력(Feedforward Actuator Effort)을 추가한다. 양의 조향 운동이 요구되면 보정된 양의 보상 토크를 추가하고 음의 운동에는 반대 부호의 보상 토크를 적용한다. 이를 통해 피드백 오차가 누적되기 전에 쿨롱 마찰의 일부를 상쇄하여 위치 제어기(Position Controller)가 전체 이탈 구동력을 생성하지 않고도 응답 성능을 향상시킬 수 있다.

정지 마찰 보상(Static-Friction Compensation)은 0에 가까운 속도에서 불연속적인 부호 기반 명령(Sign-Based Command)이 오히려 채터링(Chatter)을 발생시킬 수 있으므로 추가적인 주의가 필요하다. 데드밴드(Deadband), 부드러운 부호 근사(Smooth Sign Approximation), 속도 임계값(Velocity Threshold), 상태 의존형 보상 함수(State-Dependent Compensation Function)를 사용하면 영속도 부근에서 점진적인 전환을 제공할 수 있다. 센서 노이즈 또는 매우 작은 목표 변화가 반복적인 교번 이탈 토크와 불필요한 조향 운동을 발생시키지 않도록 보상량을 제한해야 한다.

속도 의존형 보상(Velocity-Dependent Compensation)은 점성 마찰과 스트리벡 형태의 동작을 더욱 정확하게 표현할 수 있다. 보상 모델은 일정한 방향성 항(Constant Directional Term)과 측정 또는 요구 조향 속도의 함수를 결합할 수 있다. 매우 낮은 속도에서는 보상량을 식별된 이탈 요구량에 가깝게 설정하고, 높은 속도에서는 쿨롱 및 점성 항으로 전환할 수 있다. 파라미터는 실제 액추에이터와 조향 시스템의 측정 데이터를 기반으로 식별해야 한다.

위치 데드밴드 보상(Position Deadband Compensation)은 작은 액추에이터 움직임이 즉시 바퀴 조향각 변화로 나타나지 않는 기계적 영역을 처리한다. 운동 방향이 반전되면 제어기는 비례적인 바퀴 응답을 기대하기 전에 보정된 백래시 거리(Backlash Distance)를 고려할 수 있다. 그러나 데드밴드 보상에서 단순히 급격한 위치 점프를 명령해서는 안 된다. 기계적 유격이 제거된 직후 과도한 움직임이 발생할 수 있기 때문에 변화율이 제한된 보상(Rate-Limited Compensation)이 더욱 안전한 동작을 제공한다.

히스테리시스 보상(Hysteresis Compensation)은 운동 이력을 명시적으로 표현하는 상태 기반 모델(Stateful Model)을 사용할 수도 있다. 조향 변환을 메모리가 없는 함수(Memoryless Function)로 처리하는 대신 소프트웨어는 이전 운동 방향, 반전 지점(Reversal Point), 누적 백래시(Accumulated Backlash), 탄성 상태(Elastic State)에 대한 정보를 유지한다. 보다 발전된 구현에서는 식별된 히스테리시스 모델(Identified Hysteresis Model)을 사용할 수 있지만 추가되는 복잡성은 조향 정확도와 강건성(Robustness)의 측정 가능한 향상으로 정당화되어야 한다.

피드포워드 보상을 적용하더라도 폐루프 피드백(Closed-Loop Feedback)은 계속 필요하다. 마찰은 온도, 마모(Wear), 윤활(Lubrication), 페이로드(Payload), 타이어 접촉력(Tire Contact Force), 노면, 부품 노화(Component Aging)에 따라 변화하므로 고정된 보상 맵만으로 모든 조건을 완벽하게 표현할 수 없다. 피드포워드는 예측 가능한 비선형 효과를 줄이고, 피드백 제어기는 남아 있는 오차를 보정하면서 운전 조건이 보정 조건과 달라진 경우에도 정확도를 유지해야 한다.

적분 제어(Integral Control)는 마찰로 발생하는 정상상태 오차(Steady-State Error)를 제거할 수 있지만, 조향 기구가 정지 마찰에 의해 움직이지 않는 동안 과도한 적분 동작은 와인드업(Windup)을 발생시킬 수 있다. 제어기는 스틱션 상태에서 큰 명령을 누적한 후 움직임이 갑자기 시작될 때 오버슈트(Overshoot)를 발생시킬 수 있다. 안티 와인드업 로직(Anti-Windup Logic), 조건부 적분(Conditional Integration), 적분 제한(Integral Limit), 마찰 인지형 적분기 활성화(Friction-Aware Integrator Activation)를 사용하면 이러한 동작을 줄이고 작은 조향각 목표 주변의 정착 성능을 향상시킬 수 있다.

미분 또는 속도 피드백(Derivative or Velocity Feedback)은 이탈 이후의 감쇠(Damping)를 향상시킬 수 있지만 조향 변화율 추정값은 측정 노이즈에 민감하다. 필터 설계(Filter Design)는 노이즈 제거와 제어 지연 사이의 균형을 유지해야 한다. 마찰 보상은 조향 속도의 부호와 크기에 의존하는 경우가 많기 때문에 노이즈가 많은 변화율 추정은 보상 방향의 반복적인 전환을 발생시킬 수 있다. 히스테리시스, 필터링, 최소 속도 임계값(Minimum-Velocity Threshold)을 사용하여 영속도 부근의 방향 검출을 안정화할 수 있다.

방향 반전(Direction Reversal)은 백래시와 히스테리시스가 가장 뚜렷하게 나타나는 영역이므로 명시적인 소프트웨어 처리가 필요하다. 제어기는 요구 또는 측정된 조향 속도의 부호가 변경될 때 반전을 검출하고 일시적인 보상 상태(Temporary Compensation State)로 전환할 수 있다. 이 단계에서 소프트웨어는 보정된 프리로드(Preload) 또는 백래시 보상을 적용하면서 실제 바퀴 응답을 모니터링한 후 정상 위치 추종(Normal Position Tracking)으로 복귀할 수 있다.

모델 오차(Model Error)가 위험한 조향 명령을 생성하지 않도록 보상 제한(Compensation Limit)이 필요하다. 최대 추가 토크, 전류, 위치 오프셋(Position Offset), 피드포워드 기여량(Feedforward Contribution)은 정상 제어기 출력과 독립적으로 제한되어야 한다. 결합된 최종 명령 역시 액추에이터 및 차량 수준의 조향 제한을 만족해야 한다. 안전 감독(Safety Supervision)은 보상 항 자체가 본질적으로 안전하다고 가정하지 않고 최종적으로 명령된 움직임을 평가해야 한다.

윤활유 점도(Lubricant Viscosity), 씰 마찰(Seal Friction), 모터 특성, 기계적 유격은 저온 시동(Cold Startup)과 충분히 가열된 운전 상태 사이에서 크게 달라질 수 있으므로 온도 모니터링(Temperature Monitoring)을 통해 보상 성능을 향상시킬 수 있다. 보정에는 온도 의존형 마찰 파라미터(Temperature-Dependent Friction Parameter) 또는 별도의 운전 영역을 포함할 수 있다. 온도 보상은 제한된 범위에서 적용해야 하며 온도 측정값을 사용할 수 없거나 유효하지 않은 경우 정의된 보수적 동작(Conservative Behavior)으로 전환해야 한다.

기계적 마모(Mechanical Wear)는 조향 시스템의 사용 수명 동안 백래시와 마찰 특성을 점진적으로 변화시킬 수 있다. 진단 소프트웨어는 이탈 전류(Breakaway Current), 방향 반전 시 위치 오차, 히스테리시스 폭, 보상 요구량(Compensation Demand)의 장기적인 변화를 모니터링할 수 있다. 이러한 값이 증가하면 윤활 성능 저하, 기어 마모, 조인트 풀림(Joint Looseness), 얼라인먼트 문제(Alignment Problem), 액추에이터 열화를 나타낼 수 있으며 추종 성능이 허용할 수 없는 수준으로 저하되기 전에 예지 정비(Predictive Maintenance)를 지원할 수 있다.

충분한 관측 가능성(Observability)이 확보되는 경우 적응형 보상(Adaptive Compensation)을 통해 운전 중 일부 파라미터를 갱신할 수 있다. 예를 들어 반복적인 저속 조향 반전을 이용하여 현재의 백래시 또는 이탈 토크에 대한 정보를 얻을 수 있다. 적응은 천천히 수행되고 검증된 범위 내에서 유지되어야 하며 기본 보정값(Baseline Calibration Value)을 보존해야 한다. 실제 기계적 고장이 적응형 모델에 무기한 흡수되어 정상적인 노화로 잘못 처리되어서는 안 된다.

처리된 조향 상태(Processed Steering State)는 마찰 보상 및 진단과 관련된 정보를 제공해야 한다. 유용한 신호에는 요구 조향각, 측정 조향각, 위치 오차(Position Error), 조향 속도, 운동 방향, 반전 상태(Reversal State), 피드포워드 보상량, 모터 전류, 추정 토크(Estimated Torque), 온도, 포화 상태(Saturation State)가 포함된다. 이러한 신호는 상위 진단 기능이 일반적인 추종 오차와 비선형 기계 동작을 구분하는 데 도움이 된다.

소프트웨어 검증(Software Verification)은 영속도 동작, 작은 조향 보정, 저속 스윕, 방향 반전, 대조향각 운동(Large-Angle Motion), 보상 포화(Compensation Saturation), 온도 의존형 파라미터, 센서 노이즈, 액추에이터 지연을 시험해야 한다. 시뮬레이션 모델(Simulation Model)은 정지 마찰, 쿨롱 마찰, 점성 효과(Viscous Effect), 백래시, 히스테리시스를 포함하여 실제 차량 시험 전에 보상 알고리즘을 체계적으로 평가할 수 있도록 해야 한다.

하드웨어 인 더 루프 시험(Hardware-in-the-Loop Testing)은 알려진 마찰 또는 백래시 특성을 주입하면서 액추에이터 부하, 센서 양자화(Sensor Quantization), 통신 타이밍, 제어기 실행을 재현할 수 있다. 이후 실제 벤치 시험(Physical Bench Testing)을 통해 이탈 토크, 정상 조향 구동력(Steady Steering Effort), 방향 반전 응답, 반복성(Repeatability)을 측정해야 한다. 보상을 적용한 결과와 적용하지 않은 결과를 비교하면 알고리즘이 진동이나 과도한 액추에이터 부하를 발생시키지 않으면서 추종 성능을 향상시키는지 객관적으로 확인할 수 있다.

차량 수준 검증(Vehicle-Level Validation)은 보상이 실제 경로 추종 성능에 미치는 영향을 평가해야 한다. 직선 주행 보정(Straight-Line Correction), 저속 곡선, 주차 기동(Parking Maneuver), 반복적인 S자 선회(S-Turn), 작은 조향 방향 반전은 조향 명령이 작은 오차 영역을 반복적으로 통과하기 때문에 마찰과 히스테리시스의 영향이 가장 뚜렷하게 나타나는 유용한 시험 시나리오이다. 평가 지표에는 횡방향 경로 오차(Cross-Track Error), 조향 오차, 진동, 정착 시간(Settling Time), 액추에이터 전류가 포함될 수 있다.

따라서 강건한 조향 마찰 및 히스테리시스 보상 설계(Robust Steering Friction and Hysteresis Compensation Design)는 기계적 특성화(Mechanical Characterization), 방향 의존형 보정(Direction-Dependent Calibration), 피드포워드 마찰 보상, 백래시 처리(Backlash Handling), 폐루프 피드백, 안티 와인드업 보호(Anti-Windup Protection), 온도 인지(Temperature Awareness), 제한된 적응(Bounded Adaptation), 진단, 체계적인 검증을 통합해야 한다. 예측 가능한 비선형성을 보상하면서 실제 기계적 고장을 숨기지 않도록 설계함으로써 조향 시스템은 전체 운용 수명 동안 더욱 부드럽고 반복 가능한 제어 성능을 확보할 수 있다.

##  

## 04.08 Steering Diagnostics: Sensor Fault / Redundancy Switch [w/Code]

![](images/image8.png){width="7.268055555555556in" height="7.268055555555556in"}

Steering diagnostics continuously determine whether steering commands, sensors, actuators, communication channels, and control software remain trustworthy enough for the current operating mode. Because steering directly influences vehicle trajectory, diagnostic functions should detect faults before they develop into uncontrolled wheel motion. The diagnostic architecture therefore combines signal validation, cross-checking, fault classification, redundancy management, and controlled transitions to degraded operation.

Sensor diagnostics begin with basic electrical and numerical validation. Steering-angle, motor-position, torque, current, and steering-rate signals should be checked for valid range, supply or interface faults, impossible values, frozen data, excessive noise, and unexpected discontinuities. Digital signals additionally require monitoring of communication status, counters, timestamps, checksums, and diagnostic flags provided by the sensor itself.

Range checking determines whether a measured value remains within physically and electrically possible limits. A steering-angle value beyond the mechanical travel of the system is clearly invalid, but values inside the absolute range can still be incorrect. Diagnostic software should therefore combine absolute range checks with rate-of-change, temporal consistency, operating-state, and command-response checks rather than treating any numerically plausible value as trustworthy.

Rate and gradient monitoring detect signals that change faster than the physical steering mechanism can move. If a sensor reports a large angle transition within one control cycle while actuator position and motor motion remain nearly unchanged, the measurement may contain a communication error or sensor fault. Rate thresholds should include realistic actuator dynamics and sampling intervals so that valid high-rate steering maneuvers are not incorrectly classified as faults.

Stuck-signal detection identifies sensors that remain constant when the steering system is known to be moving. A fixed steering-angle measurement may initially appear valid because it remains inside the permitted range. By comparing the signal with actuator commands, motor position, torque, vehicle yaw response, or another steering sensor, the diagnostic function can determine whether the unchanged value is physically consistent with current vehicle behavior.

Redundant steering sensors provide independent information that can be compared for fault detection. Two steering-angle sensors may use different sensing elements, electrical channels, power supplies, communication paths, or mounting locations. Redundancy is most effective when common-cause failures are minimized, because two nominally redundant sensors that depend on the same power source or communication interface may fail simultaneously.

A basic redundancy monitor compares Sensor A and Sensor B and calculates disagreement as e_AB=\|angle_A-angle_B\|. If the difference remains below a calibrated threshold, both measurements may be considered consistent. A temporary threshold crossing can be filtered using persistence time or hysteresis, while sustained disagreement should initiate fault isolation. Thresholds should account for sensor accuracy, quantization, mechanical tolerance, and dynamic delay.

Two sensors alone can detect disagreement but cannot always identify which sensor is incorrect. Additional analytical information is therefore valuable. Motor encoder position, actuator displacement, steering kinematics, yaw rate, lateral acceleration, or estimated vehicle curvature can provide independent evidence. Diagnostic logic can compare multiple relationships and assign confidence to each sensor instead of arbitrarily selecting one channel whenever disagreement occurs.

Triple-redundant sensing can support majority voting when sufficiently independent measurements are available. If two channels agree within tolerance while the third deviates significantly, the inconsistent channel can be isolated and removed from control. Majority voting should still consider correlated faults and measurement quality, because numerical agreement between two channels does not automatically guarantee that both represent the true physical steering state.

Analytical redundancy uses mathematical relationships instead of additional identical hardware. For example, road-wheel angle can be estimated from motor encoder position and calibrated steering ratio, while vehicle yaw rate can provide a slower independent consistency check. Analytical estimates usually have different uncertainty and bandwidth from direct sensors, so they are particularly useful for fault confirmation and degraded operation rather than exact replacement in every condition.

Fault detection and fault isolation should be treated as separate functions. Detection determines that available information is inconsistent, while isolation determines which component or channel is most likely faulty. Immediate switching after the first mismatch can transfer control from a healthy sensor to a faulty backup. Diagnostic logic should collect sufficient evidence, consider fault persistence, and evaluate channel health before changing the active measurement source.

Each sensor channel can maintain a health state such as valid, suspect, failed, recovering, or unavailable. A suspect state allows temporary abnormalities to be observed without immediately removing the channel, while a failed state prevents its use in safety-critical control. Recovery should require stable valid measurements for a defined period so that an intermittent sensor cannot repeatedly enter and leave the active control path.

Redundancy switching transfers the steering controller from a primary measurement source to a validated backup source. The switch should be deterministic and should not introduce a discontinuity into the feedback signal. Before switching, the software can compare offsets, timestamps, rates, and validity states between channels. If necessary, bumpless-transfer logic can align the replacement signal with the previous control state while preserving the true physical steering angle.

A redundancy manager can centralize sensor selection and expose one validated steering state to the control software. The manager receives raw or processed channels, diagnostic states, quality information, and synchronization status, then selects the appropriate source according to predefined rules. This prevents individual controllers from implementing different fault-selection logic and creates a consistent interface between sensing, diagnostics, and steering control.

Signal quality can be represented explicitly rather than using only a binary valid or invalid flag. Quality information may include confidence level, estimated uncertainty, age, redundancy status, calibration validity, and current fault state. Higher-level controllers can then reduce speed or restrict steering authority when measurement quality deteriorates, even before the steering signal becomes completely unavailable.

Time synchronization is critical when redundant sensors are compared during dynamic steering. Two correct sensors sampled at different times can appear to disagree significantly during rapid wheel movement. Diagnostic processing should therefore consider timestamps, sensor latency, bus delay, and filtering delay. Signals may be time-aligned or compared using rate-dependent thresholds so that timing differences are not mistaken for physical sensor faults.

Communication faults must be distinguished from sensing-element faults when possible. A sensor may be healthy while CAN, CAN FD, Ethernet, or another communication path is interrupted. Sequence counters, timeout monitoring, message integrity checks, bus status, and interface diagnostics can identify transport-related failures. This distinction supports better service diagnostics and may allow switching to a redundant communication channel without replacing the sensor itself.

Power-supply monitoring is another important part of redundancy management. Sensors connected to a common supply can fail together when voltage is lost or becomes unstable. Safety-oriented architectures may use independent supply domains and monitor voltage, ground integrity, and power-good signals. Diagnostic software should understand these dependencies so that multiple simultaneous sensor faults are not incorrectly interpreted as unrelated component failures.

Fault reactions should depend on the remaining steering capability. If the primary angle sensor fails but an independent validated backup remains available, normal or limited operation may continue. If only an analytical estimate remains, vehicle speed and steering authority may need to be reduced. If no trustworthy steering-state information remains, the system should transition toward a controlled safe stop according to the vehicle safety concept.

A degraded mode should explicitly define allowable speed, steering angle, steering rate, control source, and duration of continued operation. Degradation is not simply the absence of a shutdown; it is a controlled operating configuration with reduced capability. The steering supervisor should communicate degraded status to the vehicle motion controller so that path planning, propulsion, braking, and steering constraints remain mutually consistent.

Diagnostic trouble codes and event records should identify the failed signal, fault type, detection method, active backup channel, switching time, vehicle state, and recovery status. A circular event buffer can preserve steering commands, measured angles, sensor disagreement, motor position, torque, speed, communication state, and timestamps before and after a fault. Such records are essential for root-cause analysis of intermittent field failures.

Fault recovery should be more conservative than initial fault detection. A sensor that returns to a valid numerical range immediately after a dropout should not automatically regain control authority. Recovery logic can require communication stability, acceptable disagreement, correct dynamic response, and a fault-free observation interval. Depending on the safety concept, restoration of the primary channel may also require a new operating cycle or service confirmation.

Software-in-the-loop testing should inject sensor bias, offset drift, frozen values, noise, spikes, dropouts, timing delays, corrupted messages, and disagreement between redundant channels. Tests should verify detection time, isolation accuracy, switching behavior, degraded-mode entry, recovery logic, and diagnostic recording. Boundary testing around thresholds is important because unstable fault-state transitions often occur near calibration limits.

Hardware-in-the-loop and vehicle testing should reproduce realistic sensor and communication failures while observing actual steering-controller behavior. Validation should confirm that redundancy switching does not create steering jumps, oscillation, excessive torque, or trajectory instability. Tests at different vehicle speeds and steering rates should verify that fault reactions remain predictable under both steady-state and dynamic conditions.

A robust steering diagnostic and redundancy architecture therefore combines signal validation, temporal monitoring, redundant comparison, analytical consistency checks, fault isolation, channel health management, bumpless switching, degraded control, recovery supervision, and detailed event logging. By selecting only trustworthy steering information and coordinating fault responses with vehicle-level safety control, the system can maintain predictable behavior even when individual sensors or communication channels fail.

조향 진단(Steering Diagnostics)은 조향 명령, 센서, 액추에이터, 통신 채널(Communication Channel), 제어 소프트웨어가 현재 운전 모드에서 충분히 신뢰할 수 있는 상태를 유지하는지를 지속적으로 판단한다. 조향은 차량 궤적에 직접적인 영향을 주기 때문에 진단 기능은 고장이 제어되지 않는 바퀴 움직임으로 발전하기 전에 이를 검출해야 한다. 따라서 진단 아키텍처(Diagnostic Architecture)는 신호 검증, 교차 검사(Cross-Checking), 고장 분류, 이중화 관리(Redundancy Management), 성능 저하 운전(Degraded Operation)으로의 제어된 전환을 통합한다.

센서 진단(Sensor Diagnostics)은 기본적인 전기적 및 수치적 검증에서 시작한다. 조향각, 모터 위치, 토크, 전류, 조향 변화율(Steering Rate) 신호는 유효 범위, 전원 또는 인터페이스 고장, 불가능한 값, 고정된 데이터(Frozen Data), 과도한 노이즈, 예상하지 못한 불연속을 검사해야 한다. 디지털 신호는 추가적으로 통신 상태, 카운터, 타임스탬프, 체크섬(Checksum), 센서 자체에서 제공하는 진단 플래그(Diagnostic Flag)를 모니터링해야 한다.

범위 검사(Range Checking)는 측정값이 물리적 및 전기적으로 가능한 범위 내에 유지되는지를 판단한다. 시스템의 기계적 이동 범위를 벗어난 조향각 값은 명백하게 유효하지 않지만 절대 범위 내에 있는 값도 잘못될 수 있다. 따라서 진단 소프트웨어는 수치적으로 가능한 모든 값을 신뢰하는 대신 절대 범위 검사와 변화율, 시간적 일관성(Temporal Consistency), 운전 상태, 명령-응답 검사를 함께 사용해야 한다.

변화율 및 기울기 모니터링(Rate and Gradient Monitoring)은 실제 조향 기구가 움직일 수 있는 속도보다 빠르게 변화하는 신호를 검출한다. 액추에이터 위치와 모터 움직임이 거의 변하지 않았는데 센서가 한 번의 제어 주기에서 큰 조향각 변화를 보고한다면 측정값에 통신 오류 또는 센서 고장이 존재할 수 있다. 정상적인 고속 조향 동작이 잘못 고장으로 분류되지 않도록 변화율 임계값은 실제 액추에이터 동역학(Actuator Dynamics)과 샘플링 주기(Sampling Interval)를 고려해야 한다.

고정 신호 검출(Stuck-Signal Detection)은 조향 시스템이 움직이는 것으로 알려진 상황에서 일정한 값을 계속 유지하는 센서를 식별한다. 고정된 조향각 측정값은 허용 범위 안에 있기 때문에 처음에는 정상적으로 보일 수 있다. 이 신호를 액추에이터 명령, 모터 위치, 토크, 차량 요 응답(Yaw Response) 또는 다른 조향 센서와 비교함으로써 진단 기능은 변화하지 않는 값이 현재 차량 동작과 물리적으로 일치하는지를 판단할 수 있다.

이중화 조향 센서(Redundant Steering Sensor)는 고장 검출을 위해 서로 비교할 수 있는 독립적인 정보를 제공한다. 두 개의 조향각 센서는 서로 다른 센싱 요소(Sensing Element), 전기 채널, 전원 공급장치, 통신 경로 또는 장착 위치를 사용할 수 있다. 이중화는 공통 원인 고장(Common-Cause Failure)이 최소화될 때 가장 효과적이며, 동일한 전원 또는 통신 인터페이스에 의존하는 두 센서는 명목상 이중화되어 있더라도 동시에 고장날 수 있다.

기본적인 이중화 모니터(Redundancy Monitor)는 센서 A(Sensor A)와 센서 B(Sensor B)를 비교하고 불일치 값을 e_AB=\|angle_A-angle_B\|로 계산한다. 차이가 보정된 임계값 이하로 유지되면 두 측정값이 서로 일관된 것으로 판단할 수 있다. 일시적인 임계값 초과는 지속 시간(Persistence Time) 또는 히스테리시스(Hysteresis)를 사용하여 필터링할 수 있으며, 지속적인 불일치는 고장 격리(Fault Isolation)를 시작해야 한다. 임계값은 센서 정확도, 양자화(Quantization), 기계적 공차(Mechanical Tolerance), 동적 지연(Dynamic Delay)을 고려해야 한다.

두 개의 센서만으로는 불일치를 검출할 수 있지만 어떤 센서가 잘못되었는지를 항상 식별할 수 있는 것은 아니다. 따라서 추가적인 분석 정보(Analytical Information)가 유용하다. 모터 엔코더 위치(Motor Encoder Position), 액추에이터 변위, 조향 기구학(Steering Kinematics), 요율(Yaw Rate), 횡가속도(Lateral Acceleration), 추정 차량 곡률(Estimated Vehicle Curvature)은 독립적인 근거를 제공할 수 있다. 진단 로직은 불일치가 발생할 때 임의로 하나의 채널을 선택하는 대신 여러 관계를 비교하여 각 센서에 신뢰도(Confidence)를 부여할 수 있다.

충분히 독립적인 측정값을 사용할 수 있는 경우 삼중 이중화 센싱(Triple-Redundant Sensing)은 다수결 투표(Majority Voting)를 지원할 수 있다. 두 채널이 허용 오차 내에서 일치하고 세 번째 채널이 크게 벗어나면 불일치 채널을 격리하여 제어에서 제외할 수 있다. 그러나 두 채널의 수치적 일치가 두 채널 모두 실제 물리적 조향 상태를 정확하게 나타낸다는 것을 자동으로 보장하지 않으므로 다수결 투표에서도 상관 고장(Correlated Fault)과 측정 품질을 고려해야 한다.

분석적 이중화(Analytical Redundancy)는 추가적인 동일 하드웨어 대신 수학적 관계를 사용한다. 예를 들어 모터 엔코더 위치와 보정된 조향비(Calibrated Steering Ratio)를 이용하여 실제 바퀴 조향각을 추정할 수 있으며, 차량 요율은 보다 느린 독립적인 일관성 검사를 제공할 수 있다. 분석적 추정값은 일반적으로 직접 센서와 다른 불확실성과 대역폭(Bandwidth)을 가지므로 모든 조건에서 정확한 대체값으로 사용하는 것보다 고장 확인과 성능 저하 운전에 특히 유용하다.

고장 검출(Fault Detection)과 고장 격리(Fault Isolation)는 서로 별개의 기능으로 처리해야 한다. 검출은 사용 가능한 정보가 서로 일치하지 않는다는 것을 판단하고, 격리는 어떤 구성요소 또는 채널이 고장일 가능성이 가장 높은지를 판단한다. 첫 번째 불일치가 발생한 직후 즉시 전환하면 정상 센서에서 고장난 백업 센서로 제어가 넘어갈 수도 있다. 진단 로직은 측정원을 변경하기 전에 충분한 근거를 수집하고 고장 지속성을 고려하며 각 채널의 건전성(Channel Health)을 평가해야 한다.

각 센서 채널은 정상(Valid), 의심(Suspect), 고장(Failed), 복구 중(Recovering), 사용 불가(Unavailable)와 같은 건전성 상태(Health State)를 유지할 수 있다. 의심 상태에서는 일시적인 이상을 관찰하면서 채널을 즉시 제거하지 않을 수 있으며, 고장 상태에서는 해당 채널이 안전 중요 제어(Safety-Critical Control)에 사용되는 것을 방지한다. 간헐적인 센서가 활성 제어 경로에 반복적으로 진입하고 이탈하지 않도록 복구 시에는 정의된 시간 동안 안정적이고 유효한 측정값이 유지되어야 한다.

이중화 전환(Redundancy Switching)은 조향 제어기를 주 측정원(Primary Measurement Source)에서 검증된 백업 측정원(Validated Backup Source)으로 전환한다. 전환은 결정론적(Deterministic)이어야 하며 피드백 신호에 불연속을 발생시켜서는 안 된다. 전환 전에 소프트웨어는 채널 간 오프셋, 타임스탬프, 변화율, 유효성 상태를 비교할 수 있다. 필요한 경우 무충격 전환 로직(Bumpless-Transfer Logic)을 이용하여 실제 물리적 조향각을 유지하면서 대체 신호를 이전 제어 상태와 정렬할 수 있다.

이중화 관리자(Redundancy Manager)는 센서 선택을 중앙 집중화하고 하나의 검증된 조향 상태(Validated Steering State)를 제어 소프트웨어에 제공할 수 있다. 관리자는 원시 또는 처리된 채널, 진단 상태, 품질 정보(Quality Information), 동기화 상태(Synchronization Status)를 입력받아 사전에 정의된 규칙에 따라 적절한 측정원을 선택한다. 이를 통해 개별 제어기가 서로 다른 고장 선택 로직을 구현하는 것을 방지하고 센싱, 진단, 조향 제어 사이에 일관된 인터페이스를 형성할 수 있다.

신호 품질(Signal Quality)은 단순한 유효 또는 무효 플래그만 사용하는 대신 명시적으로 표현할 수 있다. 품질 정보에는 신뢰 수준(Confidence Level), 추정 불확실성(Estimated Uncertainty), 데이터 경과 시간(Age), 이중화 상태, 보정 유효성(Calibration Validity), 현재 고장 상태가 포함될 수 있다. 상위 제어기는 이를 이용하여 조향 신호가 완전히 사용할 수 없는 상태가 되기 전에도 측정 품질이 저하되면 차량 속도를 줄이거나 조향 권한(Steering Authority)을 제한할 수 있다.

동적 조향 중 이중화 센서를 비교할 때 시간 동기화(Time Synchronization)는 매우 중요하다. 서로 다른 시점에 샘플링된 두 개의 정상 센서는 빠른 바퀴 움직임 동안 상당한 불일치를 나타내는 것처럼 보일 수 있다. 따라서 진단 처리는 타임스탬프, 센서 지연(Sensor Latency), 버스 지연(Bus Delay), 필터링 지연(Filtering Delay)을 고려해야 한다. 시간 차이가 물리적 센서 고장으로 잘못 판단되지 않도록 신호를 시간 정렬(Time Alignment)하거나 변화율 의존형 임계값을 적용할 수 있다.

가능한 경우 통신 고장(Communication Fault)은 센싱 요소 고장(Sensing-Element Fault)과 구분해야 한다. 센서 자체는 정상이어도 CAN, CAN FD, 이더넷(Ethernet) 또는 다른 통신 경로가 중단될 수 있다. 시퀀스 카운터(Sequence Counter), 타임아웃 모니터링(Timeout Monitoring), 메시지 무결성 검사(Message Integrity Check), 버스 상태, 인터페이스 진단을 통해 전송 관련 고장을 식별할 수 있다. 이러한 구분은 정비 진단(Service Diagnostics)을 향상시키고 센서 자체를 교체하지 않고도 이중화 통신 채널로 전환할 수 있도록 한다.

전원 공급 모니터링(Power-Supply Monitoring) 역시 이중화 관리의 중요한 부분이다. 공통 전원에 연결된 센서들은 전압이 손실되거나 불안정해지면 동시에 고장날 수 있다. 안전 지향 아키텍처(Safety-Oriented Architecture)는 독립적인 전원 도메인(Independent Supply Domain)을 사용하고 전압, 접지 무결성(Ground Integrity), 전원 정상 신호(Power-Good Signal)를 모니터링할 수 있다. 진단 소프트웨어는 이러한 의존 관계를 이해하여 동시에 발생한 여러 센서 고장을 서로 독립적인 부품 고장으로 잘못 해석하지 않아야 한다.

고장 대응(Fault Reaction)은 남아 있는 조향 기능(Remaining Steering Capability)에 따라 달라져야 한다. 주 조향각 센서가 고장나더라도 독립적으로 검증된 백업 센서를 사용할 수 있다면 정상 또는 제한 운전을 계속할 수 있다. 분석적 추정값만 남은 경우 차량 속도와 조향 권한을 감소시켜야 할 수 있다. 신뢰할 수 있는 조향 상태 정보가 전혀 남지 않은 경우 시스템은 차량 안전 개념(Vehicle Safety Concept)에 따라 제어된 안전 정지(Controlled Safe Stop) 상태로 전환해야 한다.

성능 저하 모드(Degraded Mode)는 허용 가능한 속도, 조향각, 조향 변화율, 제어 명령원, 지속 운전 시간을 명시적으로 정의해야 한다. 성능 저하는 단순히 시스템을 정지시키지 않는다는 의미가 아니라 기능이 제한된 제어된 운전 구성(Controlled Operating Configuration)을 의미한다. 조향 감독기(Steering Supervisor)는 성능 저하 상태를 차량 운동 제어기(Vehicle Motion Controller)에 전달하여 경로 계획(Path Planning), 추진(Propulsion), 제동(Braking), 조향 제약이 상호 일관되게 유지되도록 해야 한다.

진단 고장 코드(Diagnostic Trouble Code)와 이벤트 기록(Event Record)은 고장난 신호, 고장 유형, 검출 방법, 활성 백업 채널, 전환 시간, 차량 상태, 복구 상태를 식별해야 한다. 순환 이벤트 버퍼(Circular Event Buffer)는 고장 발생 전후의 조향 명령, 측정 조향각, 센서 불일치, 모터 위치, 토크, 속도, 통신 상태, 타임스탬프를 보존할 수 있다. 이러한 기록은 간헐적인 현장 고장(Intermittent Field Failure)의 근본 원인 분석(Root-Cause Analysis)에 필수적이다.

고장 복구(Fault Recovery)는 초기 고장 검출보다 더욱 보수적으로 처리해야 한다. 데이터 손실 이후 다시 유효한 수치 범위로 돌아온 센서가 즉시 제어 권한을 회복해서는 안 된다. 복구 로직은 통신 안정성, 허용 가능한 불일치, 올바른 동적 응답, 고장이 없는 관찰 시간(Fault-Free Observation Interval)을 요구할 수 있다. 안전 개념에 따라 주 채널 복구에는 새로운 운전 주기(New Operating Cycle) 또는 정비 확인(Service Confirmation)이 추가로 요구될 수도 있다.

소프트웨어 인 더 루프 시험(Software-in-the-Loop Testing)은 센서 바이어스(Sensor Bias), 오프셋 드리프트(Offset Drift), 고정값, 노이즈, 스파이크(Spike), 데이터 손실(Dropout), 타이밍 지연, 손상된 메시지(Corrupted Message), 이중화 채널 사이의 불일치를 주입해야 한다. 시험에서는 검출 시간, 격리 정확도, 전환 동작, 성능 저하 모드 진입, 복구 로직, 진단 기록을 검증해야 한다. 불안정한 고장 상태 전환은 보정 한계 부근에서 자주 발생하기 때문에 임계값 주변의 경계 시험(Boundary Testing)이 중요하다.

하드웨어 인 더 루프 시험(Hardware-in-the-Loop Testing)과 차량 시험(Vehicle Testing)은 실제 조향 제어기의 동작을 관찰하면서 현실적인 센서 및 통신 고장을 재현해야 한다. 검증에서는 이중화 전환이 조향각 점프(Steering Jump), 진동, 과도한 토크 또는 궤적 불안정(Trajectory Instability)을 발생시키지 않는지 확인해야 한다. 서로 다른 차량 속도와 조향 변화율에서 시험하여 정상 상태와 동적 조건 모두에서 고장 대응이 예측 가능하게 유지되는지를 검증해야 한다.

따라서 강건한 조향 진단 및 이중화 아키텍처(Robust Steering Diagnostic and Redundancy Architecture)는 신호 검증, 시간적 모니터링(Temporal Monitoring), 이중화 비교(Redundant Comparison), 분석적 일관성 검사(Analytical Consistency Check), 고장 격리, 채널 건전성 관리(Channel Health Management), 무충격 전환(Bumpless Switching), 성능 저하 제어(Degraded Control), 복구 감독(Recovery Supervision), 상세 이벤트 기록을 통합해야 한다. 신뢰할 수 있는 조향 정보만 선택하고 고장 대응을 차량 수준 안전 제어(Vehicle-Level Safety Control)와 연계함으로써 개별 센서 또는 통신 채널에 고장이 발생하더라도 시스템은 예측 가능한 동작을 유지할 수 있다.

##  

## 04.09 Outdoor AMR Steering Control SW Case

![](images/image9.png){width="7.268055555555556in" height="7.268055555555556in"}

An outdoor autonomous mobile robot requires steering control software that remains stable across pavement, gravel, grass, slopes, uneven terrain, and transitions between surfaces. Unlike an indoor AMR operating on relatively flat floors, the outdoor platform experiences tire deformation, variable friction, wheel slip, steering resistance, payload variation, and localization disturbance. The steering software must therefore combine geometric control with actuator feedback, terrain awareness, diagnostics, and safety supervision.

A representative outdoor AMR may use four or six wheels with Ackermann-type front steering, four-wheel steering, or another mechanically coordinated steering configuration. The software should isolate vehicle-level motion commands from hardware-specific steering geometry. A desired path curvature or steering target is first generated by the motion controller and then converted into individual road-wheel commands according to wheelbase, track width, steering mechanism, and operating mode.

The steering software architecture can be divided into path-tracking control, steering coordination, actuator control, sensor processing, safety supervision, and diagnostics. Path tracking determines the required vehicle curvature, while the steering coordinator converts that request into feasible wheel angles. The actuator layer closes the position or torque loop, and independent monitoring verifies that commanded and measured steering behavior remain consistent with mechanical and safety limits.

Localization provides the vehicle pose required for outdoor path tracking. GNSS RTK, IMU, LiDAR, cameras, and wheel odometry may contribute to the localization solution depending on the environment. Steering control should not assume that localization quality is constant. GNSS blockage, multipath, vibration, wheel slip, dust, rain, or poor visual features can increase uncertainty, so localization confidence should influence speed, steering aggressiveness, and degraded-mode decisions.

Pure Pursuit, Stanley control, or another geometric controller can generate the nominal steering request from the reference path and estimated vehicle pose. Controller parameters should reflect outdoor vehicle dimensions and operating speed. Adaptive lookahead is particularly useful because a short lookahead improves low-speed maneuverability while a longer lookahead can reduce oscillation during faster travel over irregular surfaces or noisy localization.

The path-tracking output should preferably be represented as desired curvature before conversion to actuator-specific commands. This creates a stable software interface between motion planning and steering hardware. For a conventional Ackermann vehicle, curvature can be converted into an equivalent steering angle and then into left and right wheel angles. A 4WS platform additionally distributes the requested curvature between front and rear steering according to the selected steering mode.

Outdoor terrain introduces lateral and longitudinal tire slip that is not represented by ideal kinematic steering models. On loose gravel, wet grass, mud, or slopes, commanded wheel angle may not generate the expected vehicle curvature. The software should compare predicted motion with measured yaw rate, lateral response, and localization trajectory. Persistent differences can indicate reduced tire-road authority and justify lower speed or modified steering control parameters.

Uneven terrain also changes the mechanical load applied to the steering actuator. A wheel encountering a rut, stone, curb edge, or high-friction patch can require substantially more torque than during normal operation. Motor current or estimated steering torque should therefore be monitored together with steering-angle error. High torque with little wheel movement can indicate mechanical obstruction, terrain loading, actuator saturation, or steering linkage problems.

Steering friction compensation improves low-speed maneuvering when tire scrub and mechanical friction create a significant dead zone. Direction-dependent feedforward torque can help overcome predictable resistance before large position error accumulates. Compensation should remain bounded because outdoor tire forces vary greatly with terrain. Closed-loop feedback must correct residual error, while safety supervision prevents compensation from producing excessive motor current or rapid wheel motion.

Hysteresis and backlash become particularly visible during repeated steering reversals on rough surfaces. The controller can detect direction changes and apply calibrated backlash compensation or preload while monitoring actual wheel response. Because suspension movement and tire deformation can resemble mechanical hysteresis, compensation should not aggressively adapt to every temporary discrepancy. Long-term parameter adaptation should remain limited and diagnostically observable.

Vehicle speed should directly influence allowable steering behavior. At low speed, relatively large steering angles may be necessary for tight maneuvering around obstacles. As speed increases, maximum steering angle, steering rate, and curvature should normally be reduced to control lateral acceleration and improve stability. A speed-dependent steering envelope provides a simple supervisory boundary independent of the path-tracking algorithm.

Payload changes can also influence steering performance. A heavily loaded outdoor AMR may exhibit increased tire deformation, higher steering torque, different suspension geometry, and slower actuator response. If payload or axle-load information is available, the steering controller can select calibrated limits or parameter sets. Even without direct load measurement, motor current, steering response, and vehicle dynamics can provide diagnostic evidence that operating conditions have changed.

Slope operation requires coordination between steering, propulsion, and braking. On cross slopes, gravitational forces can produce lateral drift, while steep longitudinal slopes can change axle loads and available tire force. The steering controller should avoid attempting to correct every deviation using aggressive steering alone. The vehicle motion supervisor should coordinate speed reduction, traction management, braking, and steering to maintain a feasible trajectory.

Obstacle avoidance can generate rapid changes in desired path curvature, particularly in confined outdoor areas. The steering interface should therefore apply feasibility checks before commands reach the actuator. Curvature, angle, rate, and acceleration limits should prevent planners from demanding steering transitions that the mechanical system cannot achieve. When a path is dynamically infeasible, the appropriate response may be to reduce speed or request replanning rather than saturating continuously.

Sensor processing is critical because steering-angle measurements are exposed to vibration, temperature variation, electrical noise, and mechanical shock. Raw measurements should be calibrated, range-checked, filtered, and time synchronized before use in feedback control. Redundant steering-angle or motor-position information can provide additional confidence, while diagnostic logic detects stuck values, implausible rates, disagreement, communication timeout, and power-related faults.

A redundancy manager can provide the controller with one validated steering state instead of exposing multiple raw sensor channels. When the primary sensor becomes suspect or failed, a verified backup can be selected using deterministic switching logic. The transition should be bumpless so that changing measurement sources does not create an artificial steering command. If only lower-confidence analytical estimation remains, vehicle speed and steering authority should be reduced.

Communication between the vehicle computer and steering actuator should be monitored for freshness and integrity. CAN, CAN FD, Ethernet, or another field network may carry target angle, measured position, torque, status, and diagnostic information. Sequence counters, timestamps, timeouts, and message integrity checks help detect stale or missing data. Communication loss should lead to a predefined steering state rather than leaving the actuator with an indefinitely valid old command.

The steering state machine can coordinate initialization, standby, autonomous control, remote control, degraded operation, emergency stop, fault-latched state, and recovery. Each state defines the permitted command source and actuator behavior. Explicit state transitions are particularly important outdoors because remote intervention, localization degradation, obstacle events, communication loss, and terrain-related steering faults may occur during the same mission.

Emergency-stop behavior should be coordinated with propulsion and braking rather than simply forcing the steering angle to zero. If an E-Stop occurs while the AMR is turning, immediate steering centering can change the stopping trajectory. Depending on vehicle speed and system architecture, steering may hold its current angle or remain controlled during deceleration before transitioning to a safe state. The behavior must be deterministic and validated experimentally.

Diagnostics should preserve the context surrounding abnormal steering events. Useful data include requested curvature, commanded and measured wheel angles, steering rate, motor current, estimated torque, vehicle speed, localization quality, terrain-related status, active sensor channel, communication state, controller mode, and fault flags. Pre-event and post-event recording allows engineers to distinguish software faults from mechanical loading or environmental disturbances.

Software-in-the-loop testing should combine the steering controller with an outdoor vehicle model containing actuator delay, friction, backlash, tire slip, sensor noise, localization error, and terrain disturbances. Fault injection can evaluate sensor dropout, excessive torque, communication timeout, steering saturation, and degraded operation. Parameter sweeps across speed, curvature, friction, and payload help identify operating regions where controller margins become insufficient.

Hardware-in-the-loop testing should connect production steering software and electronic control hardware to simulated vehicle and sensor dynamics. The test environment can reproduce actuator delays, encoder faults, communication disturbances, and changing steering loads without risking the physical robot. Real steering actuators can later be incorporated into a bench setup to measure torque, tracking accuracy, thermal behavior, backlash, and response under representative loads.

Outdoor vehicle validation should include pavement, gravel, grass, slopes, uneven surfaces, low-friction regions, tight turns, long curves, repeated reversals, obstacle avoidance, and payload variation. Evaluation metrics can include cross-track error, heading error, steering error, yaw-rate response, actuator current, oscillation, path completion, fault-detection time, and safe-stop behavior. Testing should cover both nominal operation and controlled fault scenarios.

A practical outdoor AMR steering control software design therefore integrates path tracking, Ackermann or 4WS geometry, actuator feedback, friction compensation, terrain-aware limits, sensor redundancy, diagnostics, communication supervision, degraded modes, and coordinated safety control. This layered architecture allows the same motion-planning interface to support different outdoor platforms while preserving predictable steering behavior across changing terrain, payload, speed, and fault conditions.

실외 자율이동로봇(Outdoor Autonomous Mobile Robot)은 포장도로, 자갈길, 잔디, 경사로, 불규칙 지형 및 서로 다른 노면 사이의 전환 구간에서도 안정적으로 동작하는 조향 제어 소프트웨어(Steering Control Software)를 필요로 한다. 비교적 평탄한 바닥에서 운용되는 실내 자율이동로봇(Indoor AMR)과 달리 실외 플랫폼은 타이어 변형, 가변 마찰, 휠 슬립(Wheel Slip), 조향 저항, 페이로드 변화, 위치추정 교란(Localization Disturbance)의 영향을 받는다. 따라서 조향 소프트웨어는 기하학적 제어(Geometric Control)에 액추에이터 피드백, 지형 인지(Terrain Awareness), 진단, 안전 감독(Safety Supervision)을 통합해야 한다.

대표적인 실외 자율이동로봇(Outdoor AMR)은 애커먼 방식 전륜 조향(Ackermann-Type Front Steering), 4륜 조향(Four-Wheel Steering), 또는 기타 기계적으로 연계된 조향 구성을 갖는 4륜 또는 6륜 플랫폼을 사용할 수 있다. 소프트웨어는 차량 수준 운동 명령(Vehicle-Level Motion Command)을 하드웨어 종속적인 조향 기하학(Hardware-Specific Steering Geometry)과 분리해야 한다. 요구 경로 곡률 또는 조향 목표가 먼저 운동 제어기(Motion Controller)에서 생성되고 이후 휠베이스, 윤거(Track Width), 조향 기구, 운전 모드에 따라 개별 바퀴 조향 명령으로 변환된다.

조향 소프트웨어 아키텍처(Steering Software Architecture)는 경로 추종 제어(Path-Tracking Control), 조향 협조(Steering Coordination), 액추에이터 제어(Actuator Control), 센서 처리(Sensor Processing), 안전 감독, 진단으로 구분할 수 있다. 경로 추종은 필요한 차량 곡률을 결정하고 조향 협조기는 해당 요구를 구현 가능한 바퀴 조향각으로 변환한다. 액추에이터 계층은 위치 또는 토크 루프를 폐루프 제어(Closed-Loop Control)하며, 독립적인 모니터링 기능은 명령된 조향 동작과 측정된 조향 동작이 기계적 및 안전 한계와 일치하는지를 검증한다.

위치추정(Localization)은 실외 경로 추종에 필요한 차량 자세(Vehicle Pose)를 제공한다. 환경에 따라 위성항법 실시간 이동측위(GNSS RTK), 관성측정장치(IMU), 라이다(LiDAR), 카메라, 휠 오도메트리(Wheel Odometry)가 위치추정 솔루션에 사용될 수 있다. 조향 제어는 위치추정 품질이 항상 일정하다고 가정해서는 안 된다. GNSS 차단, 다중경로(Multipath), 진동, 휠 슬립, 먼지, 비, 불충분한 시각 특징(Poor Visual Features)은 불확실성을 증가시킬 수 있으므로 위치추정 신뢰도는 속도, 조향 적극성(Steering Aggressiveness), 성능 저하 모드(Degraded Mode) 결정에 영향을 주어야 한다.

퓨어 퍼슈트(Pure Pursuit), 스탠리 제어(Stanley Control), 또는 다른 기하학적 제어기(Geometric Controller)는 기준 경로와 추정 차량 자세로부터 정상 조향 요구(Nominal Steering Request)를 생성할 수 있다. 제어기 파라미터는 실외 차량의 크기와 운전 속도를 반영해야 한다. 적응형 전방주시거리(Adaptive Lookahead)는 짧은 전방주시거리가 저속 기동성을 향상시키고 긴 전방주시거리는 불규칙 노면 또는 노이즈가 포함된 위치추정 환경에서 고속 주행 시 진동을 감소시킬 수 있기 때문에 특히 유용하다.

경로 추종 출력은 액추에이터별 명령으로 변환하기 전에 요구 곡률(Desired Curvature) 형태로 표현하는 것이 바람직하다. 이를 통해 운동 계획(Motion Planning)과 조향 하드웨어 사이에 안정적인 소프트웨어 인터페이스를 구성할 수 있다. 일반적인 애커먼 차량(Ackermann Vehicle)에서는 곡률을 등가 조향각(Equivalent Steering Angle)으로 변환한 후 좌우 바퀴 조향각으로 변환할 수 있다. 4륜 조향(4WS) 플랫폼에서는 선택된 조향 모드에 따라 요구 곡률을 전륜과 후륜 조향 사이에 추가로 분배한다.

실외 지형은 이상적인 기구학적 조향 모델(Ideal Kinematic Steering Model)에 포함되지 않는 횡방향 및 종방향 타이어 슬립(Lateral and Longitudinal Tire Slip)을 발생시킨다. 느슨한 자갈, 젖은 잔디, 진흙 또는 경사로에서는 명령된 바퀴 조향각이 예상한 차량 곡률을 생성하지 못할 수 있다. 소프트웨어는 예측된 운동을 측정 요율(Yaw Rate), 횡방향 응답(Lateral Response), 위치추정 궤적과 비교해야 한다. 지속적인 차이는 타이어-노면 제어력(Tire-Road Authority)의 감소를 의미할 수 있으며 속도 감소 또는 조향 제어 파라미터 변경의 근거가 될 수 있다.

불규칙 지형(Uneven Terrain)은 조향 액추에이터에 작용하는 기계적 부하도 변화시킨다. 바퀴가 홈(Rut), 돌, 연석 가장자리 또는 고마찰 영역을 통과하면 정상 운전보다 훨씬 높은 토크가 필요할 수 있다. 따라서 모터 전류 또는 추정 조향 토크(Estimated Steering Torque)를 조향각 오차와 함께 모니터링해야 한다. 높은 토크에도 바퀴 움직임이 거의 없다면 기계적 장애, 지형 부하(Terrain Loading), 액추에이터 포화(Actuator Saturation), 조향 링크 문제를 의미할 수 있다.

조향 마찰 보상(Steering Friction Compensation)은 타이어 스크럽(Tire Scrub)과 기계적 마찰이 상당한 데드존(Dead Zone)을 형성하는 저속 기동에서 성능을 향상시킨다. 방향 의존형 피드포워드 토크(Direction-Dependent Feedforward Torque)는 큰 위치 오차가 누적되기 전에 예측 가능한 저항을 극복하는 데 도움을 줄 수 있다. 실외 타이어 힘은 지형에 따라 크게 변화하기 때문에 보상량은 제한되어야 한다. 폐루프 피드백은 잔여 오차를 보정하고 안전 감독은 보상 기능으로 인해 과도한 모터 전류 또는 급격한 바퀴 움직임이 발생하지 않도록 해야 한다.

히스테리시스(Hysteresis)와 백래시(Backlash)는 거친 노면에서 조향 방향이 반복적으로 반전될 때 특히 명확하게 나타난다. 제어기는 방향 변화를 검출하고 실제 바퀴 응답을 모니터링하면서 보정된 백래시 보상(Backlash Compensation) 또는 프리로드(Preload)를 적용할 수 있다. 서스펜션 운동과 타이어 변형이 기계적 히스테리시스와 유사하게 나타날 수 있으므로 모든 일시적 불일치에 대해 보상 기능을 공격적으로 적응시켜서는 안 된다. 장기 파라미터 적응(Long-Term Parameter Adaptation)은 제한된 범위에서 수행되고 진단을 통해 관찰 가능해야 한다.

차량 속도는 허용 가능한 조향 동작에 직접적인 영향을 주어야 한다. 저속에서는 장애물 주변에서 좁은 회전을 수행하기 위해 비교적 큰 조향각이 필요할 수 있다. 속도가 증가하면 횡가속도를 제어하고 안정성을 향상시키기 위해 일반적으로 최대 조향각, 조향 변화율, 곡률을 감소시켜야 한다. 속도 의존형 조향 영역(Speed-Dependent Steering Envelope)은 경로 추종 알고리즘과 독립적으로 적용할 수 있는 단순한 감독 경계(Supervisory Boundary)를 제공한다.

페이로드 변화(Payload Change) 역시 조향 성능에 영향을 줄 수 있다. 무거운 하중을 적재한 실외 자율이동로봇은 타이어 변형 증가, 높은 조향 토크, 변화된 서스펜션 기하학(Suspension Geometry), 느려진 액추에이터 응답을 나타낼 수 있다. 페이로드 또는 축하중(Axle Load) 정보를 사용할 수 있다면 조향 제어기는 보정된 제한값 또는 파라미터 세트를 선택할 수 있다. 직접적인 하중 측정이 없더라도 모터 전류, 조향 응답, 차량 동역학을 통해 운전 조건의 변화를 진단할 수 있다.

경사로 운전(Slope Operation)은 조향, 추진(Propulsion), 제동(Braking) 사이의 협조를 필요로 한다. 횡경사(Cross Slope)에서는 중력에 의해 횡방향 드리프트가 발생할 수 있고 가파른 종경사에서는 축하중과 사용 가능한 타이어 힘이 변화할 수 있다. 조향 제어기는 모든 편차를 공격적인 조향만으로 보정하려 해서는 안 된다. 차량 운동 감독기(Vehicle Motion Supervisor)는 구현 가능한 궤적을 유지하도록 속도 감소, 트랙션 관리(Traction Management), 제동, 조향을 협조 제어해야 한다.

장애물 회피(Obstacle Avoidance)는 특히 제한된 실외 공간에서 요구 경로 곡률을 빠르게 변화시킬 수 있다. 따라서 조향 인터페이스는 명령이 액추에이터에 전달되기 전에 구현 가능성 검사(Feasibility Check)를 수행해야 한다. 곡률, 조향각, 변화율, 가속도 제한을 통해 플래너(Planner)가 기계 시스템에서 구현할 수 없는 조향 전환을 요구하지 못하도록 해야 한다. 경로가 동적으로 구현 불가능한 경우 지속적인 포화 상태를 유지하기보다 속도를 줄이거나 재계획(Replanning)을 요청하는 것이 적절할 수 있다.

센서 처리(Sensor Processing)는 조향각 측정값이 진동, 온도 변화, 전기적 노이즈, 기계적 충격에 노출되기 때문에 중요하다. 원시 측정값(Raw Measurement)은 피드백 제어에 사용하기 전에 보정, 범위 검사, 필터링, 시간 동기화(Time Synchronization)를 수행해야 한다. 이중화 조향각 또는 모터 위치 정보는 추가적인 신뢰도를 제공할 수 있으며 진단 로직은 고정값, 비현실적인 변화율, 불일치, 통신 타임아웃, 전원 관련 고장을 검출한다.

이중화 관리자(Redundancy Manager)는 여러 원시 센서 채널을 제어기에 직접 노출하는 대신 하나의 검증된 조향 상태(Validated Steering State)를 제공할 수 있다. 주 센서가 의심 또는 고장 상태가 되면 결정론적 전환 로직(Deterministic Switching Logic)을 이용하여 검증된 백업 센서를 선택할 수 있다. 측정원을 변경할 때 인위적인 조향 명령이 발생하지 않도록 전환은 무충격(Bumpless)으로 수행되어야 한다. 신뢰도가 낮은 분석적 추정값(Analytical Estimation)만 남은 경우 차량 속도와 조향 권한을 감소시켜야 한다.

차량 컴퓨터와 조향 액추에이터 사이의 통신은 데이터 최신성(Freshness)과 무결성(Integrity)을 모니터링해야 한다. CAN, CAN FD, 이더넷(Ethernet) 또는 다른 필드 네트워크(Field Network)를 통해 목표 조향각, 측정 위치, 토크, 상태, 진단 정보가 전달될 수 있다. 시퀀스 카운터(Sequence Counter), 타임스탬프, 타임아웃, 메시지 무결성 검사를 이용하면 오래되거나 누락된 데이터를 검출할 수 있다. 통신 손실 시 액추에이터가 오래된 명령을 무기한 유효한 것으로 유지하지 않고 사전에 정의된 조향 상태로 전환해야 한다.

조향 상태 머신(Steering State Machine)은 초기화(Initialization), 대기(Standby), 자율 제어(Autonomous Control), 원격 제어(Remote Control), 성능 저하 운전, 비상 정지(Emergency Stop), 고장 래치 상태(Fault-Latched State), 복구(Recovery)를 조정할 수 있다. 각 상태에서는 허용되는 명령원과 액추에이터 동작을 정의한다. 실외에서는 하나의 임무 중 원격 개입, 위치추정 성능 저하, 장애물 이벤트, 통신 손실, 지형 관련 조향 고장이 동시에 발생할 수 있으므로 명시적인 상태 전환이 특히 중요하다.

비상 정지 동작(Emergency-Stop Behavior)은 단순히 조향각을 0으로 강제하는 대신 추진 및 제동과 협조되어야 한다. 자율이동로봇이 선회하는 동안 비상 정지(E-Stop)가 발생하면 즉각적인 조향 중앙 복귀가 정지 궤적을 변화시킬 수 있다. 차량 속도와 시스템 아키텍처에 따라 조향은 현재 각도를 유지하거나 감속 중 제어 상태를 유지한 후 안전 상태(Safe State)로 전환할 수 있다. 이러한 동작은 결정론적으로 정의되고 실험을 통해 검증되어야 한다.

진단(Diagnostics)은 비정상적인 조향 이벤트 전후의 상황 정보를 보존해야 한다. 유용한 데이터에는 요구 곡률, 명령 및 측정 바퀴 조향각, 조향 변화율, 모터 전류, 추정 토크, 차량 속도, 위치추정 품질(Localization Quality), 지형 관련 상태, 활성 센서 채널, 통신 상태, 제어기 모드, 고장 플래그(Fault Flag)가 포함된다. 이벤트 전후 기록(Pre-Event and Post-Event Recording)을 통해 엔지니어는 소프트웨어 고장과 기계적 부하 또는 환경적 교란(Environmental Disturbance)을 구분할 수 있다.

소프트웨어 인 더 루프 시험(Software-in-the-Loop Testing)은 조향 제어기를 액추에이터 지연, 마찰, 백래시, 타이어 슬립, 센서 노이즈, 위치추정 오차, 지형 교란을 포함하는 실외 차량 모델과 결합해야 한다. 고장 주입(Fault Injection)을 통해 센서 데이터 손실, 과도한 토크, 통신 타임아웃, 조향 포화, 성능 저하 운전을 평가할 수 있다. 속도, 곡률, 마찰, 페이로드에 대한 파라미터 스윕(Parameter Sweep)은 제어기 여유도(Controller Margin)가 부족해지는 운전 영역을 식별하는 데 도움을 준다.

하드웨어 인 더 루프 시험(Hardware-in-the-Loop Testing)은 양산 조향 소프트웨어 및 전자 제어 하드웨어를 시뮬레이션된 차량 및 센서 동역학과 연결해야 한다. 시험 환경에서는 실제 로봇을 위험에 노출시키지 않고 액추에이터 지연, 엔코더 고장, 통신 교란, 변화하는 조향 부하를 재현할 수 있다. 이후 실제 조향 액추에이터를 벤치 시험(Bench Setup)에 통합하여 대표적인 부하 조건에서 토크, 추종 정확도, 열적 동작(Thermal Behavior), 백래시, 응답 특성을 측정할 수 있다.

실외 차량 검증(Outdoor Vehicle Validation)은 포장도로, 자갈, 잔디, 경사로, 불규칙 노면, 저마찰 영역, 급회전, 장거리 곡선, 반복적인 방향 반전, 장애물 회피, 페이로드 변화를 포함해야 한다. 평가 지표에는 횡방향 경로 오차(Cross-Track Error), 헤딩 오차(Heading Error), 조향 오차, 요율 응답, 액추에이터 전류, 진동, 경로 완주(Path Completion), 고장 검출 시간, 안전 정지 동작(Safe-Stop Behavior)이 포함될 수 있다. 시험은 정상 운전뿐만 아니라 제어된 고장 시나리오도 포함해야 한다.

따라서 실용적인 실외 자율이동로봇 조향 제어 소프트웨어 설계(Outdoor AMR Steering Control Software Design)는 경로 추종, 애커먼 또는 4륜 조향 기하학(Ackermann or 4WS Geometry), 액추에이터 피드백, 마찰 보상, 지형 인지형 제한(Terrain-Aware Limit), 센서 이중화, 진단, 통신 감독, 성능 저하 모드, 협조 안전 제어(Coordinated Safety Control)를 통합한다. 이러한 계층형 아키텍처(Layered Architecture)를 통해 동일한 운동 계획 인터페이스를 서로 다른 실외 플랫폼에 적용하면서 변화하는 지형, 페이로드, 속도 및 고장 조건에서도 예측 가능한 조향 동작을 유지할 수 있다.

##  

## 04.10 Differential Drive Steering SW Comparison [w/Code]

![](images/image10.png){width="7.268055555555556in" height="7.268055555555556in"}

Differential-drive steering controls vehicle direction by creating a velocity difference between the left and right drive wheels rather than mechanically changing wheel orientation. This architecture is widely used in indoor AMRs, service robots, compact UGVs, and some outdoor platforms because the mechanical structure is simple. Steering behavior, however, depends directly on traction, wheel slip, motor synchronization, and accurate wheel-speed control.

For an ideal two-wheel differential-drive model, the vehicle is represented by left and right wheel linear velocities v_L and v_R separated by track width W. Vehicle longitudinal velocity and yaw rate can be expressed as v=(v_R+v_L)/2 and omega=(v_R-v_L)/W. These equations provide a simple interface for converting planner commands expressed as linear and angular velocity into individual wheel-speed targets.

Inverse kinematics converts a desired vehicle velocity v and yaw rate omega into wheel commands using v_L=v-omega W/2 and v_R=v+omega W/2. When both wheel velocities are equal, the vehicle travels straight. A velocity difference produces a curved trajectory, while equal magnitudes with opposite signs theoretically create zero-radius rotation. This direct relationship makes differential-drive command generation computationally simple.

Ackermann steering uses a fundamentally different mechanism. Instead of controlling heading through left-right wheel-speed difference, it changes the orientation of steerable wheels so that the wheel axes approximately intersect at a common instantaneous center of rotation. Propulsion and steering are therefore more physically separated, although traction and vehicle dynamics still couple their behavior during real operation.

The software interfaces consequently differ. A differential-drive controller naturally receives desired linear and angular velocity and converts them into left and right wheel speeds or torques. Ackermann software commonly receives desired curvature or steering angle together with vehicle speed. The steering subsystem then converts curvature into road-wheel angles and controls a dedicated steering actuator while the drive subsystem manages longitudinal velocity.

Differential drive can rotate within a very small area and may support pivot turns when the mechanical design and operating surface permit them. This is advantageous for indoor logistics and confined spaces. Ackermann vehicles require a finite turning radius but generally follow rolling constraints more naturally. For outdoor operation, avoiding pivot turns can reduce tire scrub, surface damage, drivetrain loading, and localization errors caused by severe slip.

Path tracking must account for these kinematic differences. For differential drive, a controller can generate v and omega directly from position and heading errors. Pure Pursuit or other geometric methods can also produce desired curvature, which can be transformed using omega=v\*kappa. The resulting angular velocity is then distributed into left and right wheel velocities through differential-drive inverse kinematics.

For Ackermann steering, path curvature is converted primarily into steering angle using relationships such as delta=atan(L\*kappa), where L is wheelbase. Detailed steering geometry then generates individual wheel angles if required. The path-tracking controller may therefore remain largely platform-independent when its output is desired curvature, while a lower vehicle-model layer performs either differential-drive or Ackermann-specific conversion.

Low-speed maneuverability is one of the strongest advantages of differential drive. Independent wheel control allows tight turning without a separate steering linkage, steering motor, rack, or steering-angle sensor. However, the apparent mechanical simplicity transfers important responsibilities into software because heading control depends on accurate synchronization of multiple drive motors and on sufficient tire-ground traction.

Wheel-speed feedback is therefore central to differential-drive steering. Encoder measurements from the left and right sides are compared with their targets and regulated using velocity controllers. Differences in motor characteristics, tire radius, loading, battery voltage, or rolling resistance can create heading error even when identical commands are issued. Per-wheel calibration and closed-loop control are necessary for repeatable straight-line motion.

Outdoor differential-drive platforms are strongly affected by wheel slip. Turning requires the wheels to follow different trajectories, and multi-wheel skid-steer arrangements may require lateral tire scrubbing because all wheels remain mechanically parallel. On high-friction pavement this can create large motor currents and tire wear, while loose terrain may generate substantial slip. The ideal kinematic model can therefore diverge significantly from actual vehicle motion.

Ackermann steering generally produces less lateral tire scrub because the wheels are oriented approximately along their local trajectories. This can improve energy efficiency, tire life, and directional stability for larger or faster outdoor vehicles. The tradeoff is additional mechanical complexity, including steering joints, linkages, actuators, angle sensors, calibration, backlash, and steering-specific diagnostic requirements.

A six-wheel platform illustrates the distinction clearly. A six-wheel differential or skid-steer AMR can steer by controlling left-side and right-side wheel groups, simplifying mechanical steering hardware. A six-wheel Ackermann or coordinated-steering platform requires suitable steering geometry but can reduce tire scrubbing. The appropriate architecture depends on vehicle mass, tire type, terrain, turning-radius requirements, speed, payload, and surface protection constraints.

Control saturation also differs between architectures. In differential drive, requested v and omega may generate wheel velocities exceeding motor limits. The command allocator must scale or prioritize the commands while preserving the intended curvature as closely as possible. In Ackermann steering, curvature may instead exceed maximum mechanical steering angle or steering-rate capability, requiring curvature limitation, speed reduction, or path replanning.

Actuator dynamics influence steering response in both systems. Differential drive changes yaw motion through wheel acceleration and tire forces, so motor torque limits and drivetrain response directly affect turning. Ackermann systems depend on the response of a dedicated steering actuator and linkage. Software models should include these dynamics when predicting whether a requested trajectory can be physically followed within the available distance and time.

Odometry behavior also differs significantly. Differential-drive odometry commonly integrates left and right wheel encoder increments to estimate translation and rotation. This approach works well on high-traction surfaces but accumulates large heading errors during wheel slip or skid turns. Ackermann odometry can use vehicle speed and steering angle, but it also suffers from tire slip, steering calibration errors, and imperfect geometric assumptions.

Sensor fusion reduces these limitations for both architectures. IMU yaw rate, GNSS RTK, LiDAR localization, visual localization, and wheel odometry can be combined to estimate vehicle motion. For differential drive, disagreement between encoder-predicted yaw and IMU-measured yaw is a useful slip indicator. For Ackermann steering, disagreement between steering-predicted curvature and measured yaw response can similarly indicate reduced traction or steering-system error.

Diagnostics for differential drive focus strongly on wheel encoders, motor current, wheel-speed disagreement, drivetrain faults, and traction conditions. A failed encoder or motor can directly compromise both propulsion and steering. Ackermann diagnostics additionally monitor steering-angle sensors, actuator position, steering torque, tracking error, backlash, and steering communication. The separation of drive and steering functions changes the fault propagation paths.

Redundancy strategies therefore differ. A differential-drive vehicle may require redundant wheel-speed information, motor-controller monitoring, or analytical estimation using IMU and vehicle motion. An Ackermann system can use redundant steering-angle sensors or motor-position feedback while retaining independent propulsion capability. In either architecture, redundancy must include fault detection and isolation rather than simply duplicating hardware.

Safety behavior must consider the physical steering principle. For differential drive, removing propulsion torque may simultaneously remove steering authority because steering is generated by drive-wheel torque. During an emergency stop, braking and left-right torque reduction must therefore be coordinated. An Ackermann vehicle may retain steering authority independently during deceleration if the steering actuator remains powered, allowing a controlled wheel orientation while braking.

Terrain and payload influence architecture selection and controller calibration. A lightweight indoor robot on smooth flooring may exploit differential drive efficiently, while a heavy outdoor AMR carrying substantial payload can experience excessive scrub torque during skid steering. Low-pressure tires, soft soil, grass, slopes, and high vehicle mass can amplify these effects, making steering architecture an important mechanical, energy, durability, and software decision.

Software abstraction can reduce platform dependence by representing motion requests through common quantities such as desired speed and curvature. A vehicle-adaptation layer can convert these requests into left-right wheel speeds for differential drive or individual steering angles for Ackermann and 4WS systems. Higher-level planning, obstacle avoidance, localization, and mission software can then remain largely unchanged across different mobile robot configurations.

Simulation should compare the architectures using identical paths, speeds, payloads, and terrain assumptions. Useful metrics include cross-track error, heading error, turning radius, wheel slip, motor current, energy consumption, steering response, tire scrub, and fault behavior. Software-in-the-loop and hardware-in-the-loop tests can reveal where ideal kinematic advantages disappear because of actuator limits, mechanical compliance, or surface interaction.

Vehicle testing should include straight tracking, constant-radius turns, S-curves, narrow maneuvers, emergency stops, slopes, low-friction surfaces, and representative payloads. Differential-drive tests should pay particular attention to wheel-speed synchronization and slip during rotation, while Ackermann tests should examine steering-angle accuracy, actuator response, hysteresis, and geometric consistency. Results should be evaluated under equivalent operating conditions.

Differential drive and Ackermann steering therefore represent different distributions of complexity rather than a simple distinction between easy and difficult steering. Differential drive simplifies mechanical steering but places strong demands on traction and wheel-speed control, while Ackermann adds steering hardware but can provide more natural rolling behavior for larger outdoor platforms. A modular software architecture allows both approaches to share planning and safety functions while retaining control logic appropriate to their physical steering principles.

차동 구동 조향(Differential-Drive Steering)은 바퀴의 방향을 기계적으로 변경하는 대신 좌측과 우측 구동 바퀴 사이에 속도 차이를 발생시켜 차량의 진행 방향을 제어한다. 이 아키텍처는 기계적 구조가 단순하기 때문에 실내 자율이동로봇(Indoor AMR), 서비스 로봇(Service Robot), 소형 무인지상차량(UGV), 일부 실외 플랫폼에 널리 사용된다. 그러나 조향 동작은 접지력(Traction), 휠 슬립(Wheel Slip), 모터 동기화(Motor Synchronization), 정확한 휠 속도 제어에 직접적으로 의존한다.

이상적인 2륜 차동 구동 모델(Two-Wheel Differential-Drive Model)에서 차량은 윤거(Track Width) W만큼 떨어진 좌측 및 우측 바퀴의 선속도 v_L과 v_R로 표현된다. 차량의 종방향 속도와 요율(Yaw Rate)은 v=(v_R+v_L)/2 및 omega=(v_R-v_L)/W로 표현할 수 있다. 이러한 식은 선속도와 각속도로 표현된 플래너 명령(Planner Command)을 개별 휠 속도 목표로 변환하는 단순한 인터페이스를 제공한다.

역기구학(Inverse Kinematics)은 요구 차량 속도 v와 요율 omega를 v_L=v-omega W/2 및 v_R=v+omega W/2를 이용하여 휠 명령으로 변환한다. 두 바퀴 속도가 같으면 차량은 직선으로 주행한다. 속도 차이가 발생하면 곡선 궤적을 형성하며, 크기가 같고 방향이 반대인 속도는 이론적으로 회전 반경이 0인 제자리 회전(Zero-Radius Rotation)을 생성한다. 이러한 직접적인 관계로 인해 차동 구동 명령 생성은 계산적으로 단순하다.

애커먼 조향(Ackermann Steering)은 근본적으로 다른 메커니즘을 사용한다. 좌우 바퀴의 속도 차이로 헤딩(Heading)을 제어하는 대신 조향 가능한 바퀴의 방향을 변경하여 각 바퀴 축이 공통의 순간 회전 중심(Instantaneous Center of Rotation)에서 대략 교차하도록 한다. 따라서 추진(Propulsion)과 조향(Steering)이 물리적으로 더욱 분리되어 있지만 실제 운전에서는 접지력과 차량 동역학(Vehicle Dynamics)에 의해 두 기능의 동작이 여전히 상호 결합된다.

이에 따라 소프트웨어 인터페이스도 서로 다르다. 차동 구동 제어기(Differential-Drive Controller)는 일반적으로 요구 선속도와 각속도를 입력받아 좌우 휠 속도 또는 토크로 변환한다. 애커먼 조향 소프트웨어는 일반적으로 차량 속도와 함께 요구 곡률(Desired Curvature) 또는 조향각을 입력받는다. 이후 조향 서브시스템은 곡률을 실제 바퀴 조향각으로 변환하고 전용 조향 액추에이터(Dedicated Steering Actuator)를 제어하며, 구동 서브시스템은 종방향 속도를 관리한다.

차동 구동은 매우 작은 공간에서 회전할 수 있으며 기계적 설계와 운전 노면이 허용하는 경우 피벗 턴(Pivot Turn)을 지원할 수 있다. 이는 실내 물류와 제한된 공간에서 장점이 된다. 애커먼 차량은 유한한 회전 반경을 필요로 하지만 일반적으로 구름 제약(Rolling Constraint)을 보다 자연스럽게 따른다. 실외 운전에서는 피벗 턴을 피함으로써 타이어 스크럽(Tire Scrub), 노면 손상, 구동계 부하, 심한 슬립으로 발생하는 위치추정 오차를 줄일 수 있다.

경로 추종(Path Tracking)은 이러한 기구학적 차이를 고려해야 한다. 차동 구동에서는 제어기가 위치 및 헤딩 오차로부터 v와 omega를 직접 생성할 수 있다. 퓨어 퍼슈트(Pure Pursuit) 또는 다른 기하학적 방법(Geometric Method)을 이용하여 요구 곡률을 생성한 후 omega=v\*kappa를 이용해 변환할 수도 있다. 생성된 각속도는 차동 구동 역기구학을 통해 좌측 및 우측 휠 속도로 분배된다.

애커먼 조향에서는 경로 곡률이 주로 delta=atan(L\*kappa)와 같은 관계를 이용하여 조향각으로 변환되며, 여기서 L은 휠베이스(Wheelbase)이다. 필요한 경우 세부 조향 기하학을 통해 개별 바퀴 조향각을 생성한다. 따라서 경로 추종 제어기의 출력을 요구 곡률로 정의하면 해당 제어기는 대부분 플랫폼 독립적(Platform-Independent)으로 유지될 수 있으며, 하위 차량 모델 계층(Vehicle-Model Layer)에서 차동 구동 또는 애커먼 방식에 맞는 변환을 수행할 수 있다.

저속 기동성(Low-Speed Maneuverability)은 차동 구동의 가장 큰 장점 중 하나이다. 독립적인 휠 제어를 통해 별도의 조향 링크(Steering Linkage), 조향 모터, 랙(Rack), 조향각 센서 없이 좁은 회전을 수행할 수 있다. 그러나 이러한 기계적 단순성은 중요한 제어 책임을 소프트웨어로 이전한다. 헤딩 제어가 여러 구동 모터의 정확한 동기화와 충분한 타이어-노면 접지력에 의존하기 때문이다.

따라서 휠 속도 피드백(Wheel-Speed Feedback)은 차동 구동 조향의 핵심이다. 좌측 및 우측 엔코더 측정값을 각각의 목표값과 비교하고 속도 제어기(Velocity Controller)를 통해 제어한다. 모터 특성, 타이어 반경, 하중, 배터리 전압, 구름 저항(Rolling Resistance)의 차이는 동일한 명령을 적용하더라도 헤딩 오차를 발생시킬 수 있다. 반복 가능한 직선 주행을 위해서는 휠별 보정(Per-Wheel Calibration)과 폐루프 제어(Closed-Loop Control)가 필요하다.

실외 차동 구동 플랫폼은 휠 슬립의 영향을 크게 받는다. 선회 시 바퀴들은 서로 다른 궤적을 따라야 하며, 다륜 스키드 조향(Multi-Wheel Skid-Steer) 구조에서는 모든 바퀴가 기계적으로 평행한 상태를 유지하기 때문에 횡방향 타이어 스크럽이 필요할 수 있다. 고마찰 포장도로에서는 높은 모터 전류와 타이어 마모를 발생시킬 수 있으며 느슨한 지형에서는 상당한 슬립이 발생할 수 있다. 따라서 이상적인 기구학 모델과 실제 차량 운동 사이에 큰 차이가 발생할 수 있다.

애커먼 조향은 각 바퀴가 해당 바퀴의 국부적인 이동 궤적 방향과 대략 일치하도록 배향되기 때문에 일반적으로 횡방향 타이어 스크럽이 작다. 이는 크거나 빠른 실외 차량에서 에너지 효율, 타이어 수명, 방향 안정성(Directional Stability)을 향상시킬 수 있다. 반면 조향 조인트, 링크, 액추에이터, 조향각 센서, 보정(Calibration), 백래시(Backlash), 조향 전용 진단 기능이 추가되어 기계적 복잡성이 증가한다.

6륜 플랫폼(Six-Wheel Platform)은 이러한 차이를 명확하게 보여준다. 6륜 차동 또는 스키드 조향 자율이동로봇은 좌측 및 우측 휠 그룹을 제어하여 조향할 수 있으므로 기계적 조향 하드웨어가 단순해진다. 6륜 애커먼 또는 협조 조향(Coordinated Steering) 플랫폼은 적절한 조향 기하학을 필요로 하지만 타이어 스크럽을 감소시킬 수 있다. 적절한 아키텍처는 차량 질량, 타이어 유형, 지형, 회전 반경 요구사항, 속도, 페이로드(Payload), 노면 보호 제약에 따라 달라진다.

제어 포화(Control Saturation) 역시 두 아키텍처에서 서로 다르게 나타난다. 차동 구동에서는 요구 v와 omega가 모터 한계를 초과하는 휠 속도를 생성할 수 있다. 명령 할당기(Command Allocator)는 의도된 곡률을 가능한 한 유지하면서 명령을 스케일링하거나 우선순위를 결정해야 한다. 애커먼 조향에서는 곡률이 최대 기계적 조향각 또는 조향 변화율 능력을 초과할 수 있으므로 곡률 제한, 속도 감소 또는 경로 재계획(Path Replanning)이 필요하다.

액추에이터 동역학(Actuator Dynamics)은 두 시스템 모두의 조향 응답에 영향을 준다. 차동 구동은 휠 가속과 타이어 힘을 이용하여 요 운동(Yaw Motion)을 변화시키므로 모터 토크 한계와 구동계 응답이 선회 동작에 직접적인 영향을 준다. 애커먼 시스템은 전용 조향 액추에이터와 링크의 응답에 의존한다. 소프트웨어 모델은 요구된 궤적을 사용 가능한 거리와 시간 내에 물리적으로 추종할 수 있는지를 예측할 때 이러한 동역학을 포함해야 한다.

오도메트리(Odometry) 동작 역시 상당한 차이가 있다. 차동 구동 오도메트리는 일반적으로 좌우 휠 엔코더 증분값을 적분하여 이동 거리와 회전을 추정한다. 이 방법은 높은 접지력을 가진 노면에서는 효과적이지만 휠 슬립이나 스키드 턴(Skid Turn) 중에는 큰 헤딩 오차가 누적된다. 애커먼 오도메트리는 차량 속도와 조향각을 사용할 수 있지만 타이어 슬립, 조향 보정 오차, 불완전한 기하학적 가정의 영향을 받는다.

센서 융합(Sensor Fusion)은 두 아키텍처 모두에서 이러한 한계를 감소시킨다. 관성측정장치(IMU)의 요율, 위성항법 실시간 이동측위(GNSS RTK), 라이다 위치추정(LiDAR Localization), 시각 위치추정(Visual Localization), 휠 오도메트리를 결합하여 차량 운동을 추정할 수 있다. 차동 구동에서는 엔코더 기반 예측 요율과 IMU 측정 요율 사이의 불일치가 유용한 슬립 지표가 된다. 애커먼 조향에서는 조향각으로 예측한 곡률과 측정 요 응답 사이의 불일치를 통해 접지력 저하 또는 조향 시스템 오류를 유사하게 검출할 수 있다.

차동 구동의 진단(Diagnostics)은 휠 엔코더, 모터 전류, 휠 속도 불일치, 구동계 고장, 접지 상태에 중점을 둔다. 엔코더 또는 모터 하나의 고장은 추진과 조향 모두를 직접적으로 손상시킬 수 있다. 애커먼 진단에서는 추가적으로 조향각 센서, 액추에이터 위치, 조향 토크, 추종 오차, 백래시, 조향 통신을 모니터링한다. 구동과 조향 기능의 분리 여부에 따라 고장 전파 경로(Fault Propagation Path)가 달라진다.

따라서 이중화 전략(Redundancy Strategy)도 서로 다르다. 차동 구동 차량에서는 이중화 휠 속도 정보, 모터 제어기 모니터링 또는 IMU와 차량 운동을 이용한 분석적 추정(Analytical Estimation)이 필요할 수 있다. 애커먼 시스템에서는 독립적인 추진 능력을 유지하면서 이중화 조향각 센서 또는 모터 위치 피드백을 사용할 수 있다. 어느 아키텍처에서도 단순히 하드웨어를 복제하는 것에 그치지 않고 고장 검출 및 격리(Fault Detection and Isolation)를 포함해야 한다.

안전 동작(Safety Behavior)은 물리적인 조향 원리를 고려해야 한다. 차동 구동에서는 추진 토크를 제거하면 조향 자체가 구동 휠 토크에 의해 생성되기 때문에 조향 권한(Steering Authority)도 동시에 사라질 수 있다. 따라서 비상 정지(Emergency Stop) 중에는 제동과 좌우 토크 감소를 협조해야 한다. 애커먼 차량은 조향 액추에이터 전원이 유지되는 경우 감속 중에도 추진과 독립적으로 조향 권한을 유지할 수 있어 제동 과정에서 바퀴 방향을 제어할 수 있다.

지형과 페이로드는 아키텍처 선택과 제어기 보정에 영향을 준다. 평탄한 바닥에서 운용되는 경량 실내 로봇은 차동 구동을 효율적으로 활용할 수 있지만, 상당한 페이로드를 운반하는 중량 실외 자율이동로봇(Heavy Outdoor AMR)은 스키드 조향 중 과도한 스크럽 토크(Scrub Torque)를 경험할 수 있다. 저압 타이어(Low-Pressure Tire), 연약 지반, 잔디, 경사로, 높은 차량 질량은 이러한 영향을 증가시킬 수 있으므로 조향 아키텍처는 기계, 에너지, 내구성 및 소프트웨어를 함께 고려하는 중요한 설계 결정이 된다.

소프트웨어 추상화(Software Abstraction)는 요구 속도와 곡률 같은 공통 물리량을 통해 운동 요구를 표현함으로써 플랫폼 의존성을 감소시킬 수 있다. 차량 적응 계층(Vehicle-Adaptation Layer)은 이러한 요구를 차동 구동에서는 좌우 휠 속도로, 애커먼 및 4륜 조향(4WS) 시스템에서는 개별 조향각으로 변환할 수 있다. 이를 통해 상위 경로 계획, 장애물 회피, 위치추정, 임무 소프트웨어(Mission Software)는 서로 다른 이동 로봇 구성에서도 대부분 변경 없이 유지할 수 있다.

시뮬레이션(Simulation)은 동일한 경로, 속도, 페이로드, 지형 조건을 적용하여 두 아키텍처를 비교해야 한다. 유용한 평가 지표에는 횡방향 경로 오차(Cross-Track Error), 헤딩 오차, 회전 반경, 휠 슬립, 모터 전류, 에너지 소비, 조향 응답, 타이어 스크럽, 고장 동작이 포함된다. 소프트웨어 인 더 루프(Software-in-the-Loop) 및 하드웨어 인 더 루프(Hardware-in-the-Loop) 시험을 통해 액추에이터 한계, 기계적 컴플라이언스(Mechanical Compliance), 노면 상호작용으로 인해 이상적인 기구학적 장점이 사라지는 영역을 확인할 수 있다.

차량 시험(Vehicle Testing)은 직선 추종, 일정 반경 선회(Constant-Radius Turn), S자 곡선(S-Curve), 좁은 공간 기동, 비상 정지, 경사로, 저마찰 노면, 대표적인 페이로드 조건을 포함해야 한다. 차동 구동 시험에서는 휠 속도 동기화와 회전 중 슬립에 특히 주의해야 하며, 애커먼 시험에서는 조향각 정확도, 액추에이터 응답, 히스테리시스(Hysteresis), 기하학적 일관성(Geometric Consistency)을 평가해야 한다. 결과는 동일한 운전 조건에서 비교 평가해야 한다.

따라서 차동 구동과 애커먼 조향은 단순히 쉬운 조향과 어려운 조향의 차이가 아니라 시스템 복잡성을 서로 다른 영역에 분배하는 두 가지 방식이다. 차동 구동은 기계적 조향을 단순화하지만 접지력과 휠 속도 제어에 높은 요구사항을 부과하며, 애커먼 조향은 조향 하드웨어를 추가하지만 대형 실외 플랫폼에서 보다 자연스러운 구름 동작(Natural Rolling Behavior)을 제공할 수 있다. 모듈형 소프트웨어 아키텍처(Modular Software Architecture)를 적용하면 두 방식 모두 공통의 계획 및 안전 기능을 공유하면서 각각의 물리적 조향 원리에 적합한 제어 로직을 유지할 수 있다.
