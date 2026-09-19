**Volume 04 Robot Control Software**


# 08. Fault Tolerant Control

##  

## 08.01 Fault Tolerant Control Concepts: Failure Mode Classification

![](images/image1.png){width="7.268055555555556in" height="7.268055555555556in"}

Fault-tolerant control is a control-system design approach that allows a robot or autonomous machine to continue operating safely when components, sensors, actuators, communication links, or software functions experience faults. Unlike conventional control, which generally assumes that system components operate within predefined conditions, fault-tolerant control explicitly considers abnormal behavior and incorporates mechanisms that preserve stability, safety, and essential functionality.

A fault should be distinguished from a failure. A fault represents an abnormal condition or deviation within a component, signal, or function, while a failure occurs when the affected element can no longer perform its required function within specified limits. Fault-tolerant control therefore attempts to detect and manage faults before they propagate into system-level failures that could compromise motion stability, operational safety, or mission completion.

The basic fault-tolerant control process can be understood as a continuous loop connecting monitoring, fault detection, diagnosis, decision making, and control adaptation. Sensor measurements and internal controller information are monitored during operation, and observed behavior is compared with expected system behavior. When significant deviations occur, diagnostic mechanisms determine whether the deviation originates from normal uncertainty, environmental disturbance, or an actual system fault.

Fault detection identifies whether abnormal behavior exists, while fault isolation determines the location or component responsible for that behavior. Fault identification may additionally estimate properties such as fault magnitude, type, and temporal evolution. These functions are commonly grouped under fault detection and diagnosis, or FDD. Reliable FDD is important because incorrect diagnosis can cause unnecessary controller reconfiguration or conceal a developing safety-critical condition.

Faults can first be classified according to their physical origin. Sensor faults affect the information used by controllers to estimate system states and environmental conditions. Examples include encoder offsets, IMU bias drift, LiDAR degradation, corrupted force measurements, and complete sensor loss. Because feedback control depends directly on measurement quality, even a functioning actuator may generate inappropriate motion when the controller receives inaccurate sensor information.

Actuator faults directly affect the system\'s ability to generate commanded forces, torques, velocities, or positions. Typical cases include motor degradation, reduced torque capability, steering actuator malfunction, brake failure, joint locking, and unexpected actuator saturation. An actuator may remain partially functional rather than fail completely, making fault estimation particularly important when determining whether degraded operation can safely continue.

Communication faults occur when information exchanged among sensors, controllers, computing nodes, and actuators becomes unavailable, delayed, corrupted, duplicated, or temporally inconsistent. Network congestion, packet loss, connector damage, synchronization errors, and communication-interface failures can therefore influence closed-loop control. Distributed robots are especially sensitive because control functions may depend on several networked computing and sensing devices.

Computing and software faults form another important category in modern robotic systems. Processor overload, memory corruption, task deadline violations, numerical instability, software exceptions, incorrect state transitions, and failed control processes can disrupt system behavior even when mechanical hardware remains healthy. Watchdogs, process supervision, execution-time monitoring, redundant computation, and safe-state management are therefore closely connected with fault-tolerant control architecture.

Faults may also be classified according to their temporal behavior. Abrupt faults appear suddenly, such as a disconnected encoder, broken communication cable, or failed motor driver. Incipient faults develop gradually through wear, thermal degradation, calibration drift, contamination, or mechanical deterioration. Intermittent faults appear and disappear irregularly, making diagnosis difficult because the affected component may behave normally during portions of operation.

Another useful classification distinguishes additive and multiplicative faults. An additive fault introduces an unwanted value into a signal, such as a sensor bias or disturbance-like error. A multiplicative fault changes the effective relationship between input and output, such as reduced actuator effectiveness or altered sensor gain. Mathematical fault models based on these categories allow observers, estimators, and controllers to represent abnormal behavior systematically.

Fault severity can be classified according to its effect on available functionality. Minor faults may have negligible influence on mission execution, while degraded faults reduce performance without immediately violating safety constraints. Critical faults threaten system stability or safety and may require immediate reconfiguration or controlled shutdown. The classification threshold should reflect the robot\'s dynamics, operating environment, payload, and potential consequences of incorrect motion.

Faults can additionally be categorized as recoverable or non-recoverable. A recoverable fault can be compensated for through redundancy, estimation, controller adaptation, or subsystem restart. A non-recoverable fault removes capabilities that cannot be restored during operation. Fault-tolerant control must therefore determine not only what has failed but also what remaining control authority and sensing capability are available after the fault occurs.

Redundancy provides one of the fundamental mechanisms for tolerating faults. Hardware redundancy uses multiple sensors, actuators, processors, communication channels, or power sources so that another component can assume a required function. Analytical redundancy instead uses mathematical relationships, observers, physical models, or information from other sensors to estimate quantities that would otherwise depend on the faulty component.

Passive fault-tolerant control uses a controller designed to remain stable and sufficiently robust for a predefined range of faults without explicitly changing its control structure. This approach can provide rapid response because fault diagnosis and controller switching are not necessarily required. However, its tolerance is limited to faults considered during design, and maintaining robustness over a broad fault range may reduce nominal control performance.

Active fault-tolerant control explicitly detects or estimates faults and modifies control behavior according to the diagnosed condition. Reconfiguration may involve changing controller parameters, switching control laws, redistributing actuator commands, replacing faulty measurements with estimated values, modifying motion constraints, or transitioning to a degraded operating mode. Active approaches can handle broader fault scenarios but depend strongly on diagnostic accuracy and response time.

In redundant robotic systems, control allocation becomes important after an actuator fault. When several actuators can contribute to the same motion objective, the controller may redistribute force or torque commands among the remaining healthy actuators. A mobile robot with multiple independently driven wheels, for example, may preserve limited mobility after losing one drive unit if sufficient controllability and traction remain.

Sensor faults can similarly be addressed through information reconfiguration. If one sensor becomes unreliable, the state estimator may reduce its weighting, reject its measurements, or substitute information derived from other sensors and system models. This approach requires explicit consideration of observability because removing a sensor can make certain system states impossible to estimate accurately even when the remaining sensors themselves are healthy.

A central concept in fault-tolerant design is graceful degradation. The objective is not always to maintain full nominal performance after a fault. Instead, the controller may progressively reduce speed, acceleration, payload handling, workspace, autonomy level, or mission complexity while preserving safety-critical capabilities. Graceful degradation prevents the unrealistic assumption that every fault must be completely masked without affecting system performance.

Safety-oriented fault handling therefore requires defined operational states. A system may transition from normal operation to degraded operation, restricted motion, minimum-risk maneuver, emergency stop, or controlled shutdown depending on fault severity and available redundancy. These transitions should be coordinated with the motion controller so that a safety response does not itself introduce excessive acceleration, instability, collision risk, or uncontrolled mechanical loading.

Fault propagation must also be considered because a local defect can generate secondary abnormalities elsewhere in the system. A damaged wheel encoder may produce erroneous velocity estimates, which can cause incorrect motor commands, trajectory deviation, and eventually localization errors. Effective diagnosis therefore distinguishes the original fault from downstream symptoms and prevents multiple alarms from being incorrectly interpreted as independent failures.

Model-based fault diagnosis commonly uses residual signals representing differences between measured and predicted behavior. Observers, Kalman filters, parameter estimators, parity relations, and physical models can generate these residuals. Threshold logic then evaluates whether residual patterns are consistent with normal disturbances or specific faults. Robust threshold design is necessary because modeling uncertainty and sensor noise can otherwise create false alarms.

Data-driven diagnosis complements model-based methods when accurate analytical models are difficult to construct. Statistical learning, machine learning, neural networks, anomaly detection, and temporal pattern recognition can identify abnormal operating signatures from historical or simulated data. In safety-critical control, however, data-driven outputs should be integrated with physical constraints, confidence measures, validation procedures, and deterministic fallback behavior.

Fault-tolerant control must distinguish faults from disturbances and normal operating variability. Terrain irregularities, payload changes, wheel slip, external forces, temperature variation, and sensor noise may temporarily produce signals similar to component faults. Diagnosis therefore requires temporal consistency, multi-sensor correlation, model-based reasoning, and contextual information to reduce both false-positive fault declarations and dangerous missed detections.

The timing of fault response is as important as detection accuracy. A rapidly developing actuator or steering fault may require intervention within milliseconds, whereas gradual battery degradation can be managed over a much longer interval. Fault-tolerant architectures consequently use different monitoring rates and response mechanisms according to fault dynamics, control-loop frequency, system inertia, and the time available before safety margins are violated.

Designing fault-tolerant control also requires analysis of controllability and observability under fault conditions. A robot that is controllable and observable during normal operation may lose these properties after actuator or sensor removal. Engineers must therefore evaluate whether the remaining system can still regulate critical states, estimate necessary variables, and execute a safe maneuver for each fault configuration considered credible.

Failure-mode classification provides the systematic foundation for this analysis. Each relevant failure mode can be associated with its source, symptoms, severity, detectability, propagation path, remaining functionality, and required control response. Methods such as failure mode and effects analysis can support this process by connecting component-level faults with system-level consequences and helping define diagnostic coverage and mitigation requirements.

The resulting architecture should integrate fault management across sensing, estimation, planning, control, actuation, communication, and safety supervision rather than treating fault tolerance as an isolated controller feature. A diagnostic decision may alter state estimation, constrain trajectory generation, change actuator allocation, and trigger a safety state simultaneously. Clear interfaces between these functions are essential for deterministic and verifiable behavior.

Verification must evaluate both nominal performance and behavior during representative fault scenarios. Testing can include sensor disconnection, biased measurements, delayed communication, reduced actuator effectiveness, processor overload, and combinations of faults when relevant. Simulation, software-in-the-loop, hardware-in-the-loop, fault injection, and controlled physical testing provide progressively stronger evidence that the intended mitigation mechanisms operate correctly.

Ultimately, fault-tolerant control transforms failure management from an emergency reaction into an integrated control-system capability. By classifying failure modes, detecting abnormal behavior, determining remaining system capability, and adapting control objectives accordingly, robotic systems can maintain stability and essential functionality under degraded conditions while transitioning safely when continued operation is no longer technically justified.

고장 허용 제어(Fault-Tolerant Control)는 구성요소, 센서(Sensor), 액추에이터(Actuator), 통신 링크(Communication Link) 또는 소프트웨어 기능에 고장이 발생하더라도 로봇이나 자율 시스템이 안전하게 동작을 지속할 수 있도록 하는 제어 시스템 설계 접근법이다. 일반적인 제어가 시스템 구성요소가 사전에 정의된 조건 내에서 정상적으로 동작한다고 가정하는 것과 달리, 고장 허용 제어는 비정상적인 동작을 명시적으로 고려하고 안정성(Stability), 안전성(Safety), 핵심 기능을 유지하기 위한 메커니즘을 포함한다.

결함(Fault)은 고장(Failure)과 구분되어야 한다. 결함은 구성요소, 신호 또는 기능에서 발생하는 비정상적인 상태나 편차를 의미하며, 고장은 영향을 받은 요소가 규정된 한계 내에서 요구 기능을 더 이상 수행할 수 없는 상태를 의미한다. 따라서 고장 허용 제어는 결함이 시스템 수준의 고장으로 전파되어 운동 안정성(Motion Stability), 운용 안전성(Operational Safety), 임무 수행(Mission Completion)을 손상시키기 전에 이를 탐지하고 관리하는 것을 목표로 한다.

고장 허용 제어의 기본 과정은 모니터링(Monitoring), 결함 탐지(Fault Detection), 진단(Diagnosis), 의사결정(Decision Making), 제어 적응(Control Adaptation)이 연결된 연속적인 루프로 이해할 수 있다. 운용 중 센서 측정값과 내부 제어기 정보가 지속적으로 감시되며, 관측된 동작은 예상되는 시스템 동작과 비교된다. 유의미한 편차가 발생하면 진단 메커니즘은 해당 편차가 정상적인 불확실성, 환경 외란(Environmental Disturbance), 실제 시스템 결함 중 어디에서 발생했는지를 판단한다.

결함 탐지(Fault Detection)는 비정상적인 동작의 존재 여부를 식별하고, 결함 분리(Fault Isolation)는 해당 동작을 발생시킨 위치나 구성요소를 판단한다. 결함 식별(Fault Identification)은 추가적으로 결함의 크기, 유형, 시간적 변화 특성 등을 추정할 수 있다. 이러한 기능은 일반적으로 결함 탐지 및 진단(Fault Detection and Diagnosis, FDD)으로 통합되며, 잘못된 진단은 불필요한 제어기 재구성(Controller Reconfiguration)을 발생시키거나 진행 중인 안전 중요 결함을 감출 수 있으므로 신뢰성 높은 FDD가 중요하다.

결함은 먼저 물리적 발생 원인에 따라 분류할 수 있다. 센서 결함(Sensor Fault)은 제어기가 시스템 상태와 환경 조건을 추정하는 데 사용하는 정보에 영향을 미친다. 대표적인 사례로 엔코더 오프셋(Encoder Offset), 관성 측정 장치 편향 드리프트(IMU Bias Drift), 라이다 성능 저하(LiDAR Degradation), 손상된 힘 측정값, 센서의 완전한 상실 등이 있다. 피드백 제어(Feedback Control)는 측정 품질에 직접 의존하기 때문에 액추에이터가 정상이어도 부정확한 센서 정보가 입력되면 잘못된 운동이 발생할 수 있다.

액추에이터 결함(Actuator Fault)은 시스템이 명령된 힘, 토크(Torque), 속도 또는 위치를 생성하는 능력에 직접적인 영향을 미친다. 대표적인 사례에는 모터 성능 저하, 토크 생성 능력 감소, 조향 액추에이터 고장, 브레이크 고장, 관절 잠김(Joint Locking), 예상하지 못한 액추에이터 포화(Actuator Saturation) 등이 있다. 액추에이터는 완전히 고장 나기보다는 부분적으로 기능이 저하된 상태로 남을 수도 있으므로, 저하 운전(Degraded Operation)을 안전하게 지속할 수 있는지를 판단하기 위해 결함 추정이 특히 중요하다.

통신 결함(Communication Fault)은 센서, 제어기, 컴퓨팅 노드(Computing Node), 액추에이터 사이에서 교환되는 정보가 손실되거나 지연되고, 손상되거나 중복되며, 시간적으로 불일치할 때 발생한다. 네트워크 혼잡(Network Congestion), 패킷 손실(Packet Loss), 커넥터 손상, 동기화 오류(Synchronization Error), 통신 인터페이스 고장은 폐루프 제어(Closed-Loop Control)에 영향을 줄 수 있다. 분산형 로봇(Distributed Robot)은 여러 네트워크 기반 컴퓨팅 및 센싱 장치에 제어 기능이 의존하기 때문에 이러한 결함에 특히 민감하다.

컴퓨팅 및 소프트웨어 결함(Computing and Software Fault)은 현대 로봇 시스템에서 또 다른 중요한 범주를 구성한다. 프로세서 과부하(Processor Overload), 메모리 손상(Memory Corruption), 태스크 데드라인 위반(Task Deadline Violation), 수치적 불안정성(Numerical Instability), 소프트웨어 예외(Software Exception), 잘못된 상태 전이(State Transition), 제어 프로세스 실패는 기계 하드웨어가 정상인 경우에도 시스템 동작을 방해할 수 있다. 따라서 워치독(Watchdog), 프로세스 감독(Process Supervision), 실행 시간 모니터링, 중복 연산(Redundant Computation), 안전 상태 관리(Safe-State Management)는 고장 허용 제어 아키텍처와 밀접하게 연계된다.

결함은 시간적 동작 특성에 따라서도 분류할 수 있다. 급작 결함(Abrupt Fault)은 엔코더 단선, 통신 케이블 파손, 모터 드라이버 고장과 같이 갑작스럽게 발생한다. 점진 결함(Incipient Fault)은 마모, 열화(Thermal Degradation), 교정 드리프트(Calibration Drift), 오염 또는 기계적 성능 저하를 통해 서서히 발전한다. 간헐 결함(Intermittent Fault)은 불규칙적으로 발생하고 사라지기 때문에 영향을 받은 구성요소가 운용 중 일부 구간에서는 정상적으로 동작할 수 있어 진단이 더욱 어렵다.

또 다른 유용한 분류 방법은 가산 결함(Additive Fault)과 승산 결함(Multiplicative Fault)을 구분하는 것이다. 가산 결함은 센서 편향이나 외란과 유사한 오류처럼 신호에 원하지 않는 값을 추가한다. 승산 결함은 액추에이터 효율 감소나 센서 이득 변화처럼 입력과 출력 사이의 실질적인 관계를 변화시킨다. 이러한 범주를 기반으로 한 수학적 결함 모델(Mathematical Fault Model)은 관측기(Observer), 추정기(Estimator), 제어기가 비정상적인 동작을 체계적으로 표현할 수 있도록 한다.

결함 심각도(Fault Severity)는 가용 기능에 미치는 영향에 따라 분류할 수 있다. 경미한 결함(Minor Fault)은 임무 수행에 거의 영향을 주지 않을 수 있으며, 성능 저하 결함(Degraded Fault)은 안전 제약조건을 즉각적으로 위반하지 않으면서 시스템 성능을 감소시킨다. 치명적 결함(Critical Fault)은 시스템 안정성이나 안전성을 위협하며 즉각적인 재구성 또는 제어된 정지(Controlled Shutdown)가 필요할 수 있다. 분류 임계값은 로봇의 동역학, 운용 환경, 탑재 하중(Payload), 잘못된 운동이 초래할 수 있는 결과를 반영해야 한다.

결함은 복구 가능 결함(Recoverable Fault)과 복구 불가능 결함(Non-Recoverable Fault)으로도 구분할 수 있다. 복구 가능 결함은 중복성(Redundancy), 추정(Estimation), 제어기 적응(Controller Adaptation), 서브시스템 재시작을 통해 보상할 수 있다. 복구 불가능 결함은 운용 중 복원할 수 없는 기능 상실을 발생시킨다. 따라서 고장 허용 제어는 무엇이 고장 났는지만 판단하는 것이 아니라 결함 발생 이후에도 어떠한 제어 권한(Control Authority)과 센싱 능력이 남아 있는지를 판단해야 한다.

중복성(Redundancy)은 결함을 허용하기 위한 핵심적인 메커니즘 중 하나를 제공한다. 하드웨어 중복성(Hardware Redundancy)은 여러 개의 센서, 액추에이터, 프로세서, 통신 채널 또는 전원을 사용하여 하나의 구성요소가 고장 났을 때 다른 구성요소가 필요한 기능을 대신 수행하도록 한다. 반면 해석적 중복성(Analytical Redundancy)은 수학적 관계, 관측기, 물리 모델 또는 다른 센서의 정보를 활용하여 고장 난 구성요소에 의존하던 물리량을 추정한다.

수동형 고장 허용 제어(Passive Fault-Tolerant Control)는 제어 구조를 명시적으로 변경하지 않고도 사전에 정의된 결함 범위에서 안정성과 충분한 강인성(Robustness)을 유지하도록 설계된 제어기를 사용한다. 결함 진단이나 제어기 전환이 반드시 필요하지 않기 때문에 빠른 대응이 가능하다. 그러나 허용 가능한 결함은 설계 단계에서 고려된 범위로 제한되며, 광범위한 결함 조건에서 강인성을 확보하려 하면 정상 상태의 제어 성능이 저하될 수 있다.

능동형 고장 허용 제어(Active Fault-Tolerant Control)는 결함을 명시적으로 탐지하거나 추정하고 진단된 상태에 따라 제어 동작을 변경한다. 재구성(Reconfiguration)은 제어기 파라미터 변경, 제어 법칙(Control Law) 전환, 액추에이터 명령 재분배, 결함 측정값을 추정값으로 대체, 운동 제약조건 변경 또는 성능 저하 운전 모드로의 전환 등을 포함할 수 있다. 능동형 방식은 보다 다양한 결함 시나리오에 대응할 수 있지만 진단 정확도와 응답 시간에 크게 의존한다.

중복 로봇 시스템(Redundant Robotic System)에서는 액추에이터 결함 이후 제어 할당(Control Allocation)이 중요해진다. 여러 액추에이터가 동일한 운동 목표 달성에 기여할 수 있다면 제어기는 정상적으로 동작하는 나머지 액추에이터 사이에 힘 또는 토크 명령을 재분배할 수 있다. 예를 들어 여러 개의 독립 구동 휠을 가진 이동 로봇은 하나의 구동 장치를 상실하더라도 충분한 제어 가능성(Controllability)과 접지력(Traction)이 남아 있다면 제한적인 이동 능력을 유지할 수 있다.

센서 결함 역시 정보 재구성(Information Reconfiguration)을 통해 대응할 수 있다. 하나의 센서가 신뢰할 수 없는 상태가 되면 상태 추정기(State Estimator)는 해당 센서의 가중치를 낮추거나 측정값을 제거하고, 다른 센서와 시스템 모델에서 얻은 정보로 이를 대체할 수 있다. 이러한 접근에서는 관측 가능성(Observability)을 명시적으로 고려해야 하는데, 센서를 제거하면 나머지 센서가 정상적으로 동작하더라도 특정 시스템 상태를 정확하게 추정할 수 없게 될 수 있기 때문이다.

고장 허용 설계에서 핵심적인 개념 중 하나는 점진적 성능 저하(Graceful Degradation)이다. 결함이 발생한 이후 항상 정상 상태의 전체 성능을 유지하는 것이 목표가 되는 것은 아니다. 대신 제어기는 안전에 중요한 기능을 유지하면서 속도, 가속도, 탑재물 처리 능력, 작업 공간(Workspace), 자율화 수준(Autonomy Level), 임무 복잡도를 단계적으로 낮출 수 있다. 점진적 성능 저하는 모든 결함을 시스템 성능 변화 없이 완전히 감춰야 한다는 비현실적인 가정을 방지한다.

따라서 안전 중심 결함 처리(Safety-Oriented Fault Handling)를 위해서는 명확하게 정의된 운용 상태가 필요하다. 시스템은 결함의 심각도와 사용 가능한 중복성에 따라 정상 운전(Normal Operation)에서 성능 저하 운전(Degraded Operation), 제한 운동(Restricted Motion), 최소 위험 기동(Minimum-Risk Maneuver), 비상 정지(Emergency Stop), 제어된 종료(Controlled Shutdown) 상태로 전환될 수 있다. 이러한 전환은 안전 대응 자체가 과도한 가속, 불안정성, 충돌 위험 또는 제어되지 않은 기계적 하중을 발생시키지 않도록 운동 제어기(Motion Controller)와 조정되어야 한다.

결함 전파(Fault Propagation)도 고려해야 한다. 국부적인 결함이 시스템의 다른 부분에서 이차적인 이상 현상을 발생시킬 수 있기 때문이다. 손상된 휠 엔코더(Wheel Encoder)는 잘못된 속도 추정값을 생성할 수 있으며, 이는 잘못된 모터 명령, 궤적 편차(Trajectory Deviation), 궁극적으로 위치 추정(Localization) 오류로 이어질 수 있다. 따라서 효과적인 진단은 최초의 결함과 이후 발생한 증상을 구분하고, 여러 경보가 서로 독립적인 고장으로 잘못 해석되는 것을 방지해야 한다.

모델 기반 결함 진단(Model-Based Fault Diagnosis)은 일반적으로 측정된 동작과 예측된 동작 사이의 차이를 나타내는 잔차 신호(Residual Signal)를 사용한다. 관측기(Observer), 칼만 필터(Kalman Filter), 파라미터 추정기(Parameter Estimator), 패리티 관계(Parity Relation), 물리 모델(Physical Model)을 이용하여 이러한 잔차를 생성할 수 있다. 이후 임계값 논리(Threshold Logic)는 잔차 패턴이 정상적인 외란인지 특정 결함과 일치하는지를 평가한다. 모델링 불확실성과 센서 노이즈로 인한 오경보(False Alarm)를 방지하기 위해서는 강인한 임계값 설계가 필요하다.

데이터 기반 진단(Data-Driven Diagnosis)은 정확한 해석적 모델을 구축하기 어려운 경우 모델 기반 방법을 보완한다. 통계적 학습(Statistical Learning), 기계학습(Machine Learning), 신경망(Neural Network), 이상 탐지(Anomaly Detection), 시간 패턴 인식(Temporal Pattern Recognition)은 과거 데이터나 시뮬레이션 데이터에서 비정상적인 운용 특성을 식별할 수 있다. 그러나 안전 중요 제어에서는 데이터 기반 출력이 물리적 제약조건, 신뢰도 지표(Confidence Measure), 검증 절차, 결정론적 대체 동작(Deterministic Fallback Behavior)과 함께 통합되어야 한다.

고장 허용 제어는 결함을 외란(Disturbance) 및 정상적인 운용 변동성과 구분할 수 있어야 한다. 지형 불규칙성, 탑재 하중 변화, 휠 슬립(Wheel Slip), 외력, 온도 변화, 센서 노이즈는 일시적으로 구성요소 결함과 유사한 신호를 발생시킬 수 있다. 따라서 진단 과정에서는 오탐지(False Positive)와 위험한 미탐지(Missed Detection)를 모두 줄이기 위해 시간적 일관성, 다중 센서 상관관계(Multi-Sensor Correlation), 모델 기반 추론, 상황 정보(Contextual Information)를 활용해야 한다.

결함 대응 시점은 탐지 정확도만큼 중요하다. 빠르게 진행되는 액추에이터 또는 조향 결함은 수 밀리초 이내의 개입이 필요할 수 있지만, 점진적인 배터리 성능 저하는 훨씬 긴 시간 범위에서 관리할 수 있다. 따라서 고장 허용 아키텍처는 결함 동역학(Fault Dynamics), 제어 루프 주파수(Control-Loop Frequency), 시스템 관성(System Inertia), 안전 여유가 위반되기까지 사용 가능한 시간에 따라 서로 다른 모니터링 주기와 대응 메커니즘을 사용한다.

고장 허용 제어를 설계할 때에는 결함 조건에서의 제어 가능성(Controllability)과 관측 가능성(Observability)도 분석해야 한다. 정상 운전에서 제어 및 관측 가능한 로봇이라도 액추에이터나 센서가 제거된 이후에는 이러한 특성을 상실할 수 있다. 따라서 엔지니어는 고려 대상인 각각의 결함 구성에 대해 남아 있는 시스템이 핵심 상태를 계속 제어하고, 필요한 변수를 추정하며, 안전 기동(Safe Maneuver)을 수행할 수 있는지를 평가해야 한다.

고장 모드 분류(Failure-Mode Classification)는 이러한 분석을 위한 체계적인 기반을 제공한다. 각각의 관련 고장 모드는 발생 원인, 증상, 심각도, 탐지 가능성(Detectability), 전파 경로, 잔존 기능(Remaining Functionality), 필요한 제어 대응과 연계할 수 있다. 고장 형태 및 영향 분석(Failure Mode and Effects Analysis, FMEA)과 같은 방법은 구성요소 수준의 결함을 시스템 수준의 결과와 연결하고 진단 범위(Diagnostic Coverage)와 완화 요구사항(Mitigation Requirement)을 정의하는 데 활용할 수 있다.

최종적인 아키텍처는 고장 허용 기능을 독립적인 제어기 기능으로 취급하기보다 센싱(Sensing), 추정(Estimation), 계획(Planning), 제어(Control), 구동(Actuation), 통신(Communication), 안전 감독(Safety Supervision) 전반에 걸쳐 결함 관리를 통합해야 한다. 하나의 진단 결과가 상태 추정을 변경하고, 궤적 생성을 제한하며, 액추에이터 할당을 변경하고, 동시에 안전 상태를 활성화할 수 있다. 이러한 기능 사이의 명확한 인터페이스는 결정론적이고 검증 가능한 시스템 동작을 구현하는 데 필수적이다.

검증(Verification)은 정상 상태의 성능뿐만 아니라 대표적인 결함 시나리오에서의 시스템 동작까지 평가해야 한다. 시험에는 센서 단절, 편향된 측정값, 통신 지연, 액추에이터 효율 감소, 프로세서 과부하 및 필요한 경우 복합 결함(Combined Fault)을 포함할 수 있다. 시뮬레이션(Simulation), 소프트웨어 인 더 루프(Software-in-the-Loop, SIL), 하드웨어 인 더 루프(Hardware-in-the-Loop, HIL), 결함 주입(Fault Injection), 통제된 실물 시험을 통해 의도된 결함 완화 메커니즘이 올바르게 작동한다는 근거를 단계적으로 확보할 수 있다.

궁극적으로 고장 허용 제어(Fault-Tolerant Control)는 고장 관리를 단순한 비상 대응에서 통합된 제어 시스템 능력으로 전환한다. 고장 모드를 체계적으로 분류하고, 비정상 동작을 탐지하며, 남아 있는 시스템 능력을 판단하고, 이에 따라 제어 목표를 적응시킴으로써 로봇 시스템은 성능이 저하된 조건에서도 안정성과 필수 기능을 유지하면서 지속적인 운용이 기술적으로 더 이상 타당하지 않을 경우 안전한 상태로 전환할 수 있다.

##  

## 08.02 Sensor Fault Detection: Residual Generation / Thresholding [w/Code]

![](images/image2.png){width="7.268055555555556in" height="7.268055555555556in"}

Sensor fault detection is a fundamental function of fault-tolerant control because feedback controllers depend on reliable measurements of position, velocity, acceleration, force, orientation, temperature, pressure, and environmental conditions. A faulty sensor can generate physically plausible but incorrect information, making detection more difficult than recognizing complete signal loss. The objective is therefore to identify abnormal measurement behavior before it significantly affects estimation or control.

A common sensor fault detection architecture compares measured sensor outputs with independently predicted or reconstructed values. The difference between these quantities is called a residual. Under normal operating conditions, the residual should remain close to an expected statistical or deterministic range. When a sensor develops a bias, drift, gain error, excessive noise, frozen output, or complete failure, the residual changes in a characteristic manner that can provide evidence of abnormal behavior.

For a measured output y(k) and predicted output ŷ(k), a basic residual can be represented as r(k) = y(k) − ŷ(k). Although this expression is simple, effective residual generation requires the prediction to represent normal system behavior accurately. The predicted value may be obtained from a physical model, state observer, Kalman filter, redundant sensor, analytical relationship, or data-driven estimator depending on the available system information.

Model-based residual generation uses mathematical knowledge of system dynamics to predict sensor measurements. A state-space model can estimate internal states from control inputs and available measurements, after which predicted sensor outputs are calculated. If the model represents normal behavior sufficiently well, discrepancies between predictions and measurements reveal possible faults. Model uncertainty must nevertheless be considered because imperfect dynamics can generate residuals even when every sensor is healthy.

Observers are widely used for residual generation because they reconstruct system states from measured inputs and outputs. A Luenberger observer, nonlinear observer, disturbance observer, or specialized fault detection observer may be designed so that estimation errors remain small during normal operation but become sensitive to particular sensor faults. Observer design can also reduce sensitivity to disturbances and modeling errors while preserving sensitivity to faults that must be detected.

Kalman-filter-based residual generation is especially useful when measurements contain stochastic noise. The filter predicts the system state and measurement distribution and then compares the actual measurement with its prediction. This difference, commonly called an innovation, behaves as a statistically characterized residual under valid modeling assumptions. Changes in innovation magnitude, mean, variance, or correlation can therefore indicate sensor degradation or unexpected measurement behavior.

Analytical redundancy provides another mechanism for generating residuals without installing physically duplicated sensors. Known relationships among system variables are used to test whether measurements remain mutually consistent. For example, wheel velocity, vehicle acceleration, motor speed, and estimated displacement may provide overlapping information about robot motion. A violation of these relationships can reveal a sensor fault even when no identical backup sensor is available.

Physical redundancy instead compares measurements from multiple sensors observing the same or closely related quantity. Dual encoders, redundant IMUs, multiple temperature sensors, or overlapping ranging sensors can provide direct consistency checks. Simple voting can detect an outlier when sufficient redundancy exists, while weighted fusion can account for different sensor accuracies. The major limitation is additional hardware cost, weight, power consumption, communication load, and common-mode failure risk.

Residual generation should ideally produce signals that are sensitive to faults but insensitive to normal disturbances. This requirement creates a fundamental design tradeoff. Increasing residual sensitivity can improve early detection but may also increase false alarms caused by noise, model mismatch, terrain changes, payload variation, or transient motion. Robust residual design therefore attempts to separate fault signatures from uncertainty through filtering, normalization, decoupling, and statistical characterization.

Residual preprocessing can substantially improve detection reliability. Raw residuals may be filtered to suppress high-frequency measurement noise, normalized according to expected variance, or transformed into energy and statistical indicators over a time window. Techniques such as moving averages, root-mean-square values, cumulative sums, and exponentially weighted statistics allow weak but persistent faults to become more visible than they would be in individual instantaneous samples.

Thresholding converts residual information into a fault decision. The simplest approach uses a fixed threshold, declaring a fault when \|r(k)\| exceeds a predetermined value. Fixed thresholds are computationally inexpensive and easy to verify, making them useful for embedded robotic controllers. However, a threshold that is sufficiently large to avoid false alarms during aggressive motion may become too insensitive to detect smaller faults during steady operation.

Adaptive thresholds address this limitation by changing detection boundaries according to operating conditions. Thresholds may depend on robot velocity, acceleration, payload, temperature, estimated noise variance, terrain condition, controller mode, or model uncertainty. During highly dynamic operation, a wider threshold may tolerate expected residual variation, while during stable operation a narrower threshold can improve sensitivity to small biases or gradual sensor degradation.

Statistical thresholding treats residuals as random variables characterized during healthy operation. If the expected residual distribution is known or estimated, confidence intervals can define acceptable regions. Normalized innovation squared, chi-square tests, likelihood ratios, and related statistical measures can determine whether observed residual behavior is sufficiently improbable under the healthy hypothesis to justify a fault indication. This provides a systematic relationship between sensitivity and false-alarm probability.

Single-sample threshold violations should not always trigger an immediate fault declaration. Short disturbances, communication jitter, electromagnetic interference, or abrupt robot motion can temporarily produce large residuals. Persistence logic requires the residual to remain abnormal for a specified duration or number of samples. Debouncing and hysteresis similarly prevent repeated transitions between healthy and faulty states when residual values fluctuate near the threshold boundary.

Different sensor fault modes create different residual signatures. A constant bias tends to shift the residual mean, while a slowly developing drift produces a progressive trend. Increased measurement noise raises residual variance, a frozen sensor can generate increasing disagreement as the physical state changes, and complete signal loss may produce invalid values or communication timeouts. Fault detection algorithms can exploit these characteristics rather than relying exclusively on residual magnitude.

Detecting incipient faults is particularly challenging because their initial residual magnitude may remain below conventional thresholds. Trend analysis, cumulative sum methods, sequential probability tests, parameter estimation, and temporal machine-learning models can accumulate evidence over time. The objective is to detect persistent degradation early enough for maintenance or control adaptation without interpreting ordinary long-term variations as faults.

Residual evaluation can also use multiple residual channels simultaneously. A residual vector may contain inconsistencies associated with several sensors, state variables, or physical relationships. The pattern of activated residuals forms a fault signature that supports fault isolation. If one encoder fault affects a specific subset of residuals while an IMU fault affects another subset, the diagnostic system can distinguish the likely source rather than merely reporting a generic sensor anomaly.

Fault isolation requires residuals with appropriate structural properties. Ideally, each residual responds strongly to selected faults while remaining insensitive to others. Structured residual sets, dedicated observers, parity-space methods, and directional residual analysis can create distinguishable signatures. In complex robotic systems, complete isolation may not always be possible, so the diagnostic system should represent uncertainty instead of forcing an unsupported classification.

Multi-sensor consistency checking is especially valuable for autonomous robots. Wheel odometry can be compared with visual odometry, LiDAR localization, GNSS velocity, and inertial measurements. A disagreement does not automatically identify which sensor is faulty, but consistency relationships provide evidence that can be combined over time. Context is essential because wheel slip, GNSS multipath, visual degradation, or LiDAR occlusion may temporarily alter sensor reliability without hardware failure.

Sensor fault detection should therefore interact closely with state estimation. Once a measurement is suspected of being unreliable, the estimator may reduce its weighting, increase its measurement covariance, reject it temporarily, or replace it with an analytical estimate. This transition should be gradual when appropriate because immediately removing a sensor after a weak diagnostic indication can destabilize estimation or reduce observability more severely than the original measurement fault.

Diagnostic confidence provides a useful interface between detection and control. Instead of generating only a binary healthy-or-faulty decision, the detection system can estimate fault probability, confidence, residual severity, or sensor health level. The controller and estimator can then select responses proportional to diagnostic certainty. Low confidence may initiate enhanced monitoring, while high-confidence critical faults may trigger sensor exclusion and immediate operational restrictions.

Threshold design must explicitly consider false positives and false negatives. A false positive incorrectly declares a healthy sensor faulty and can unnecessarily reduce system capability. A false negative allows corrupted measurements to continue influencing estimation and control. The acceptable balance depends on safety consequences, available redundancy, sensor criticality, robot dynamics, and whether the system can safely operate after removing the suspected sensor.

Fault detection latency is another important design parameter. Filtering and persistence logic improve robustness but delay detection. For a fast steering, joint-position, or stabilization loop, excessive delay may allow a sensor fault to destabilize the robot before mitigation begins. Conversely, extremely aggressive detection may react to harmless transients. Detection time must therefore be designed relative to system dynamics and the maximum tolerable duration of erroneous feedback.

Real-time implementation requires deterministic computational behavior. Residual generation and threshold evaluation often execute at the same rate as estimation or control loops, ranging from relatively slow environmental monitoring to high-frequency motor and inertial sensing. Algorithms should have bounded execution time, defined numerical behavior, timestamp consistency, and clear handling of missing or stale data so that the diagnostic function itself does not introduce control uncertainty.

Communication integrity is closely related to sensor fault detection in distributed architectures. A valid sensor may appear faulty if packets are delayed, reordered, duplicated, or lost. Timestamp checks, sequence counters, timeout monitoring, data-validity flags, and synchronization diagnostics help distinguish sensor hardware faults from communication faults. This distinction is important because replacing a healthy sensor measurement does not correct a network problem affecting the underlying data path.

Common-mode failures require additional consideration when redundant sensors share power supplies, mounting structures, clocks, communication buses, environmental exposure, or software drivers. Two sensors may agree with each other while both produce incorrect measurements. Independent physical principles, diverse sensing technologies, separate communication paths, and analytical consistency checks can reduce dependence on simple agreement between nominally redundant devices.

Sensor health management should maintain diagnostic state across time rather than treating every sample independently. A sensor may progress through states such as healthy, suspicious, degraded, failed, and recovering. State transitions can incorporate residual magnitude, persistence, confidence, communication status, and recovery evidence. Recovery should normally require stronger evidence than a single valid measurement to prevent unstable switching between faulty and healthy classifications.

Verification of sensor fault detection requires controlled fault injection across representative operating conditions. Bias, drift, gain changes, increased noise, frozen values, intermittent loss, delayed measurements, and complete disconnection can be introduced in simulation, software-in-the-loop, hardware-in-the-loop, or physical testing. Performance should be evaluated using detection rate, false-alarm rate, isolation accuracy, detection latency, and the resulting effect on state estimation and control.

The final objective is not merely to generate an alarm but to provide trustworthy information for fault-tolerant control. Residual generation converts sensor behavior into diagnostic evidence, while thresholding determines when that evidence is sufficiently significant to require action. When combined with robust estimation, temporal reasoning, redundancy, and appropriate control reconfiguration, sensor fault detection allows a robotic system to preserve reliable state information and maintain safe operation despite measurement degradation.

센서 결함 탐지(Sensor Fault Detection)는 피드백 제어기(Feedback Controller)가 위치, 속도, 가속도, 힘, 자세, 온도, 압력 및 환경 조건에 대한 신뢰성 있는 측정값에 의존하기 때문에 고장 허용 제어(Fault-Tolerant Control)의 핵심 기능이다. 결함이 발생한 센서는 물리적으로 그럴듯하지만 잘못된 정보를 생성할 수 있어 완전한 신호 손실보다 탐지가 어려울 수 있다. 따라서 중요한 목표는 비정상적인 측정 동작이 상태 추정이나 제어에 큰 영향을 미치기 전에 이를 식별하는 것이다.

일반적인 센서 결함 탐지 아키텍처(Sensor Fault Detection Architecture)는 측정된 센서 출력과 독립적으로 예측하거나 재구성한 값을 비교한다. 이 두 값의 차이를 잔차(Residual)라고 한다. 정상 운전 조건에서 잔차는 예상되는 통계적 또는 결정론적 범위 내에 유지되어야 한다. 센서에 편향(Bias), 드리프트(Drift), 이득 오류(Gain Error), 과도한 노이즈, 출력 고정 또는 완전 고장이 발생하면 잔차가 특징적으로 변화하며 비정상 동작을 판단할 수 있는 근거를 제공한다.

측정 출력 y(k)와 예측 출력 ŷ(k)에 대해 기본적인 잔차는 r(k) = y(k) − ŷ(k)로 표현할 수 있다. 이 식은 단순하지만 효과적인 잔차 생성(Residual Generation)을 위해서는 예측값이 정상적인 시스템 동작을 정확하게 나타내야 한다. 예측값은 사용 가능한 시스템 정보에 따라 물리 모델(Physical Model), 상태 관측기(State Observer), 칼만 필터(Kalman Filter), 중복 센서(Redundant Sensor), 해석적 관계(Analytical Relationship) 또는 데이터 기반 추정기(Data-Driven Estimator)를 통해 얻을 수 있다.

모델 기반 잔차 생성(Model-Based Residual Generation)은 시스템 동역학(System Dynamics)에 대한 수학적 지식을 이용하여 센서 측정값을 예측한다. 상태 공간 모델(State-Space Model)은 제어 입력과 사용 가능한 측정값을 이용해 내부 상태를 추정하고, 이를 바탕으로 예측 센서 출력을 계산할 수 있다. 모델이 정상 동작을 충분히 정확하게 표현한다면 예측값과 측정값 사이의 불일치는 잠재적인 결함을 나타낼 수 있다. 그러나 불완전한 동역학 모델은 모든 센서가 정상인 경우에도 잔차를 발생시킬 수 있으므로 모델 불확실성(Model Uncertainty)을 고려해야 한다.

관측기(Observer)는 측정된 입력과 출력으로부터 시스템 상태를 재구성할 수 있기 때문에 잔차 생성에 널리 사용된다. 루엔버거 관측기(Luenberger Observer), 비선형 관측기(Nonlinear Observer), 외란 관측기(Disturbance Observer), 특수 결함 탐지 관측기(Fault Detection Observer)는 정상 운전에서는 추정 오차를 작게 유지하면서 특정 센서 결함에는 민감하게 반응하도록 설계할 수 있다. 또한 관측기 설계를 통해 탐지해야 하는 결함에 대한 민감도를 유지하면서 외란과 모델링 오차에 대한 민감도를 줄일 수 있다.

칼만 필터 기반 잔차 생성(Kalman-Filter-Based Residual Generation)은 측정값에 확률적 노이즈(Stochastic Noise)가 포함되어 있을 때 특히 유용하다. 필터는 시스템 상태와 측정값의 분포를 예측한 다음 실제 측정값과 예측값을 비교한다. 일반적으로 혁신값(Innovation)이라고 하는 이 차이는 모델링 가정이 유효할 경우 통계적으로 특성이 정의된 잔차로 동작한다. 따라서 혁신값의 크기, 평균, 분산 또는 상관관계 변화는 센서 성능 저하나 예상하지 못한 측정 동작을 나타낼 수 있다.

해석적 중복성(Analytical Redundancy)은 물리적으로 중복된 센서를 설치하지 않고 잔차를 생성할 수 있는 또 다른 방법을 제공한다. 시스템 변수 사이에 알려진 관계를 이용하여 측정값들이 상호 일관성을 유지하는지를 검사한다. 예를 들어 휠 속도(Wheel Velocity), 차량 가속도, 모터 속도, 추정 이동거리는 로봇 운동에 대해 서로 중첩되는 정보를 제공할 수 있다. 이러한 관계가 위반되면 동일한 백업 센서가 존재하지 않더라도 센서 결함을 탐지할 수 있다.

반면 물리적 중복성(Physical Redundancy)은 동일하거나 밀접하게 관련된 물리량을 관측하는 여러 센서의 측정값을 비교한다. 이중 엔코더(Dual Encoder), 중복 관성 측정 장치(Redundant IMU), 다중 온도 센서 또는 관측 영역이 중첩되는 거리 센서는 직접적인 일관성 검사를 제공할 수 있다. 충분한 중복성이 존재하면 단순 투표(Simple Voting)를 통해 이상값을 탐지할 수 있으며, 가중 융합(Weighted Fusion)을 통해 센서별 정확도 차이를 고려할 수도 있다. 주요 한계는 추가적인 하드웨어 비용, 무게, 전력 소비, 통신 부하 및 공통 원인 고장(Common-Mode Failure)의 위험이다.

이상적인 잔차 생성은 결함에는 민감하지만 정상적인 외란에는 둔감한 신호를 생성해야 한다. 이러한 요구조건은 근본적인 설계 절충관계(Design Tradeoff)를 발생시킨다. 잔차 민감도를 높이면 결함을 조기에 탐지할 가능성이 증가하지만 노이즈, 모델 불일치, 지형 변화, 탑재 하중 변화 또는 과도 운동(Transient Motion)에 의한 오경보(False Alarm)도 증가할 수 있다. 따라서 강인한 잔차 설계(Robust Residual Design)는 필터링, 정규화(Normalization), 디커플링(Decoupling), 통계적 특성화를 통해 결함 특성과 불확실성을 분리하려고 한다.

잔차 전처리(Residual Preprocessing)는 탐지 신뢰성을 크게 향상시킬 수 있다. 원시 잔차(Raw Residual)는 고주파 측정 노이즈를 억제하기 위해 필터링하거나 예상 분산에 따라 정규화할 수 있으며, 일정한 시간 구간에 대한 에너지 또는 통계 지표로 변환할 수도 있다. 이동 평균(Moving Average), 제곱평균제곱근(Root-Mean-Square, RMS), 누적합(Cumulative Sum), 지수 가중 통계(Exponentially Weighted Statistics) 등의 기법을 사용하면 개별 순간 샘플에서는 명확하지 않은 약하지만 지속적인 결함을 더욱 효과적으로 탐지할 수 있다.

임계값 처리(Thresholding)는 잔차 정보를 결함 판단으로 변환한다. 가장 단순한 방법은 고정 임계값(Fixed Threshold)을 사용하여 \|r(k)\|가 사전에 정의된 값을 초과하면 결함을 선언하는 것이다. 고정 임계값은 계산 비용이 낮고 검증하기 쉬워 임베디드 로봇 제어기(Embedded Robotic Controller)에 유용하다. 그러나 격렬한 운동 중 발생하는 오경보를 방지할 만큼 임계값을 크게 설정하면 정상적인 정속 운전에서는 작은 결함을 탐지하기 어려워질 수 있다.

적응형 임계값(Adaptive Threshold)은 운전 조건에 따라 탐지 경계를 변경하여 이러한 한계를 보완한다. 임계값은 로봇 속도, 가속도, 탑재 하중, 온도, 추정 노이즈 분산, 지형 조건, 제어기 모드 또는 모델 불확실성에 따라 변화할 수 있다. 동적인 운전 상태에서는 예상되는 잔차 변화를 허용하도록 넓은 임계값을 사용할 수 있으며, 안정적인 운전 상태에서는 좁은 임계값을 적용하여 작은 편향이나 점진적인 센서 성능 저하에 대한 민감도를 높일 수 있다.

통계적 임계값 처리(Statistical Thresholding)는 정상 운전 중 특성화된 잔차를 확률 변수(Random Variable)로 취급한다. 예상되는 잔차 분포를 알고 있거나 추정할 수 있다면 신뢰 구간(Confidence Interval)을 이용하여 허용 영역을 정의할 수 있다. 정규화 혁신 제곱(Normalized Innovation Squared), 카이제곱 검정(Chi-Square Test), 우도비(Likelihood Ratio) 및 관련 통계 기법을 통해 관측된 잔차가 정상 상태 가설(Healthy Hypothesis)에서 발생하기 어려운 수준인지를 판단할 수 있으며, 이를 통해 민감도와 오경보 확률 사이의 관계를 체계적으로 설정할 수 있다.

단일 샘플의 임계값 위반이 항상 즉각적인 결함 선언으로 이어져서는 안 된다. 짧은 외란, 통신 지터(Communication Jitter), 전자기 간섭(Electromagnetic Interference), 급격한 로봇 운동은 일시적으로 큰 잔차를 발생시킬 수 있다. 지속성 논리(Persistence Logic)는 잔차가 지정된 시간 또는 샘플 수 동안 비정상 상태를 유지하도록 요구한다. 디바운싱(Debouncing)과 히스테리시스(Hysteresis) 역시 잔차가 임계값 부근에서 변동할 때 정상과 결함 상태가 반복적으로 전환되는 현상을 방지한다.

서로 다른 센서 결함 모드(Sensor Fault Mode)는 서로 다른 잔차 특성을 생성한다. 일정한 편향은 잔차 평균을 이동시키는 경향이 있으며, 서서히 발생하는 드리프트는 점진적인 추세를 만든다. 측정 노이즈 증가는 잔차 분산을 증가시키고, 센서 출력 고정(Frozen Sensor)은 실제 물리 상태가 변화하면서 불일치를 증가시킬 수 있으며, 완전한 신호 손실은 유효하지 않은 값이나 통신 타임아웃(Communication Timeout)을 발생시킬 수 있다. 결함 탐지 알고리즘은 단순한 잔차 크기에만 의존하지 않고 이러한 특성을 활용할 수 있다.

초기 결함(Incipient Fault)의 탐지는 초기 잔차 크기가 일반적인 임계값보다 작을 수 있기 때문에 특히 어렵다. 추세 분석(Trend Analysis), 누적합 방법(Cumulative Sum Method), 순차 확률 검정(Sequential Probability Test), 파라미터 추정(Parameter Estimation), 시간적 기계학습 모델(Temporal Machine-Learning Model)을 사용하면 시간에 따라 결함 증거를 누적할 수 있다. 목표는 일반적인 장기 변화를 결함으로 잘못 판단하지 않으면서 유지보수 또는 제어 적응이 가능한 충분히 이른 시점에 지속적인 성능 저하를 탐지하는 것이다.

잔차 평가는 여러 개의 잔차 채널(Residual Channel)을 동시에 사용할 수도 있다. 잔차 벡터(Residual Vector)는 여러 센서, 상태 변수 또는 물리적 관계와 관련된 불일치 정보를 포함할 수 있다. 활성화된 잔차의 패턴은 결함 시그니처(Fault Signature)를 형성하여 결함 분리(Fault Isolation)를 지원한다. 특정 엔코더 결함이 특정 잔차 집합에 영향을 미치고 IMU 결함이 다른 잔차 집합에 영향을 미친다면, 진단 시스템은 단순히 일반적인 센서 이상을 보고하는 것이 아니라 가능한 결함 발생원을 구분할 수 있다.

결함 분리를 위해서는 적절한 구조적 특성을 가진 잔차가 필요하다. 이상적으로 각각의 잔차는 선택된 결함에는 강하게 반응하면서 다른 결함에는 둔감해야 한다. 구조화된 잔차 집합(Structured Residual Set), 전용 관측기(Dedicated Observer), 패리티 공간 기법(Parity-Space Method), 방향성 잔차 분석(Directional Residual Analysis)을 통해 서로 구별 가능한 결함 시그니처를 생성할 수 있다. 복잡한 로봇 시스템에서는 완전한 결함 분리가 항상 가능하지 않으므로 진단 시스템은 근거가 부족한 분류를 강제하기보다 불확실성을 표현할 수 있어야 한다.

다중 센서 일관성 검사(Multi-Sensor Consistency Checking)는 자율 로봇에서 특히 중요하다. 휠 오도메트리(Wheel Odometry)는 비주얼 오도메트리(Visual Odometry), 라이다 위치 추정(LiDAR Localization), GNSS 속도, 관성 측정값과 비교할 수 있다. 측정값 사이의 불일치가 어느 센서에 결함이 있는지를 자동으로 의미하는 것은 아니지만, 일관성 관계는 시간에 따라 결합할 수 있는 진단 근거를 제공한다. 휠 슬립, GNSS 다중경로(GNSS Multipath), 영상 품질 저하 또는 라이다 가림(LiDAR Occlusion)이 하드웨어 고장 없이도 일시적으로 센서 신뢰도를 변화시킬 수 있으므로 상황 정보(Context)가 중요하다.

따라서 센서 결함 탐지는 상태 추정(State Estimation)과 긴밀하게 상호작용해야 한다. 특정 측정값의 신뢰성이 낮다고 판단되면 상태 추정기는 해당 센서의 가중치를 낮추거나 측정 공분산(Measurement Covariance)을 증가시키고, 측정값을 일시적으로 제거하거나 해석적 추정값으로 대체할 수 있다. 약한 진단 신호만으로 센서를 즉시 제거하면 원래의 측정 결함보다 상태 추정을 더욱 불안정하게 만들거나 관측 가능성(Observability)을 심각하게 저하시킬 수 있으므로 필요한 경우 이러한 전환은 점진적으로 수행되어야 한다.

진단 신뢰도(Diagnostic Confidence)는 결함 탐지와 제어 사이의 유용한 인터페이스를 제공한다. 탐지 시스템은 단순히 정상 또는 결함이라는 이진 판단(Binary Decision)만 생성하는 대신 결함 확률, 신뢰도, 잔차 심각도 또는 센서 건전성 수준(Sensor Health Level)을 추정할 수 있다. 제어기와 상태 추정기는 진단 확실성에 비례하여 대응 방식을 선택할 수 있다. 낮은 신뢰도에서는 강화된 모니터링을 시작하고, 높은 신뢰도의 치명적 결함에서는 센서를 제외하고 즉각적인 운용 제한을 적용할 수 있다.

임계값 설계에서는 오탐(False Positive)과 미탐(False Negative)을 명시적으로 고려해야 한다. 오탐은 정상 센서를 결함으로 잘못 판단하여 불필요하게 시스템 기능을 감소시킬 수 있다. 반대로 미탐은 손상된 측정값이 계속해서 상태 추정과 제어에 영향을 미치도록 한다. 허용 가능한 균형은 안전 결과, 사용 가능한 중복성, 센서의 중요도, 로봇 동역학, 의심되는 센서를 제거한 이후 시스템이 안전하게 운전할 수 있는지 여부에 따라 결정된다.

결함 탐지 지연시간(Fault Detection Latency)도 중요한 설계 파라미터이다. 필터링과 지속성 논리는 강인성을 향상시키지만 탐지를 지연시킨다. 빠르게 동작하는 조향, 관절 위치 또는 안정화 제어 루프에서는 지나친 지연으로 인해 완화 조치가 시작되기 전에 센서 결함이 로봇을 불안정하게 만들 수 있다. 반대로 지나치게 공격적인 탐지는 무해한 과도 현상에 반응할 수 있다. 따라서 탐지 시간은 시스템 동역학과 잘못된 피드백을 허용할 수 있는 최대 시간을 기준으로 설계해야 한다.

실시간 구현(Real-Time Implementation)을 위해서는 결정론적인 계산 동작(Deterministic Computational Behavior)이 필요하다. 잔차 생성과 임계값 평가는 비교적 느린 환경 모니터링부터 고주파 모터 및 관성 센싱에 이르기까지 상태 추정 또는 제어 루프와 동일한 주기로 실행되는 경우가 많다. 알고리즘은 제한된 실행 시간(Bounded Execution Time), 정의된 수치 동작, 일관된 타임스탬프(Timestamp), 누락되거나 오래된 데이터에 대한 명확한 처리 방식을 가져야 하며, 이를 통해 진단 기능 자체가 제어 불확실성을 발생시키지 않도록 해야 한다.

분산형 아키텍처(Distributed Architecture)에서는 통신 무결성(Communication Integrity)이 센서 결함 탐지와 밀접하게 연관된다. 정상적인 센서라도 패킷이 지연되거나 순서가 바뀌고, 중복되거나 손실되면 결함이 있는 것처럼 보일 수 있다. 타임스탬프 검사, 시퀀스 카운터(Sequence Counter), 타임아웃 모니터링, 데이터 유효성 플래그(Data-Validity Flag), 동기화 진단을 이용하면 센서 하드웨어 결함과 통신 결함을 구분할 수 있다. 이러한 구분은 정상 센서의 측정값을 대체하는 것만으로는 데이터 경로의 네트워크 문제를 해결할 수 없기 때문에 중요하다.

공통 원인 고장(Common-Mode Failure)은 중복 센서가 전원 공급 장치, 장착 구조, 클록(Clock), 통신 버스, 환경 노출 또는 소프트웨어 드라이버를 공유할 때 추가적으로 고려해야 한다. 두 센서가 서로 일치하면서도 모두 잘못된 측정값을 생성할 수 있기 때문이다. 독립적인 물리 원리, 서로 다른 센싱 기술(Diverse Sensing Technology), 분리된 통신 경로, 해석적 일관성 검사를 사용하면 명목상 중복된 장치 간의 단순한 일치에 대한 의존성을 줄일 수 있다.

센서 건전성 관리(Sensor Health Management)는 각각의 샘플을 독립적으로 판단하기보다 시간에 따라 진단 상태를 유지해야 한다. 센서는 정상(Healthy), 의심(Suspicious), 성능 저하(Degraded), 고장(Failed), 복구 중(Recovering)과 같은 상태를 거칠 수 있다. 상태 전이는 잔차 크기, 지속 시간, 신뢰도, 통신 상태 및 복구 근거를 함께 고려할 수 있다. 결함과 정상 상태 사이에서 불안정하게 반복 전환되는 것을 방지하기 위해 일반적으로 복구 판단에는 단일 정상 측정값보다 강한 근거가 필요하다.

센서 결함 탐지의 검증(Verification)은 대표적인 운전 조건에서 통제된 결함 주입(Fault Injection)을 통해 수행해야 한다. 편향, 드리프트, 이득 변화, 노이즈 증가, 출력 고정, 간헐적인 신호 손실, 지연된 측정값 및 완전한 연결 단절을 시뮬레이션(Simulation), 소프트웨어 인 더 루프(Software-in-the-Loop, SIL), 하드웨어 인 더 루프(Hardware-in-the-Loop, HIL) 또는 실제 시스템 시험에서 주입할 수 있다. 성능은 탐지율(Detection Rate), 오경보율(False-Alarm Rate), 결함 분리 정확도(Isolation Accuracy), 탐지 지연시간, 그리고 상태 추정 및 제어에 미치는 영향을 기준으로 평가해야 한다.

최종적인 목표는 단순히 경보를 생성하는 것이 아니라 고장 허용 제어(Fault-Tolerant Control)에 신뢰할 수 있는 정보를 제공하는 것이다. 잔차 생성(Residual Generation)은 센서 동작을 진단 근거로 변환하고, 임계값 처리(Thresholding)는 해당 근거가 조치를 요구할 만큼 충분히 유의미한지를 판단한다. 강인한 상태 추정, 시간적 추론(Temporal Reasoning), 중복성, 적절한 제어 재구성(Control Reconfiguration)과 결합하면 센서 결함 탐지는 측정 성능이 저하되는 상황에서도 로봇 시스템이 신뢰성 있는 상태 정보를 유지하고 안전한 운전을 지속할 수 있도록 한다.

##  

## 08.03 Actuator Fault Detection and Isolation [w/Code]

![](images/image3.png){width="7.268055555555556in" height="7.268055555555556in"}

Actuator fault detection and isolation is a critical function in robotic fault-tolerant control because actuators directly convert control commands into physical forces, torques, velocities, and motion. A failure in a motor, drive electronics, steering mechanism, brake, hydraulic element, or robotic joint can immediately reduce control authority. The diagnostic system must therefore detect abnormal actuation rapidly and determine which actuator or subsystem is responsible.

Unlike many sensor faults, actuator faults influence both internal measurements and the physical response of the robot. A controller may command a specific torque or velocity while the actuator produces a smaller, delayed, saturated, or completely different response. Detection therefore requires comparison between commanded behavior and observed system dynamics rather than relying only on electrical status signals from the actuator hardware.

Common actuator fault modes include complete loss of actuation, partial loss of effectiveness, unintended output, mechanical locking, excessive friction, saturation, delayed response, and intermittent operation. Electric drives can additionally experience winding faults, inverter faults, encoder-related commutation problems, thermal derating, or power-supply limitations. Each mode produces different consequences for controllability and requires different diagnostic evidence.

A useful mathematical representation models actuator effectiveness through the relationship u_f = Λu + f_a, where u is the commanded actuator input, u_f is the effective input applied to the plant, Λ represents multiplicative effectiveness loss, and f_a represents an additive actuator fault. A healthy actuator has effectiveness close to its nominal value, while reduced coefficients indicate partial degradation and values approaching zero represent severe loss of authority.

Residual generation provides the basic mechanism for identifying discrepancies caused by actuator faults. The expected robot response is calculated using control commands and a nominal dynamic model, while the actual response is obtained from sensors. Differences in acceleration, velocity, position, force, current, or torque can then form residual signals. Persistent residuals inconsistent with expected disturbances provide evidence that commanded actuation is not being correctly realized.

Model-based observers are particularly useful because actuator faults enter system dynamics through control inputs. An observer predicts state evolution from commanded inputs and measured outputs. If an actuator loses effectiveness, the measured motion begins to diverge from the predicted trajectory. Observer residuals can therefore reveal the presence of abnormal actuation even when the actuator controller itself continues reporting apparently valid command execution.

Unknown input observers and disturbance observers can improve discrimination between actuator faults and external disturbances. A mobile robot encountering a slope, collision force, or increased rolling resistance may exhibit motion similar to reduced motor torque. Diagnostic observers can be structured to reject or estimate selected disturbances while retaining sensitivity to actuator abnormalities, reducing the probability that environmental effects are incorrectly classified as hardware faults.

Electrical measurements provide another important diagnostic channel for motor-driven systems. Commanded torque, motor current, phase current, bus voltage, rotor speed, temperature, and estimated torque can be compared for physical consistency. High current combined with unexpectedly low acceleration may indicate mechanical resistance or drivetrain damage, whereas low current despite a large torque command may suggest inverter, power-stage, communication, or motor-control failure.

Mechanical information complements electrical diagnostics. Joint position, wheel velocity, shaft acceleration, load-cell measurements, force-torque sensors, vibration, and temperature can reveal actuator degradation that electrical measurements alone cannot isolate. Combining electrical and mechanical residuals allows the diagnostic system to distinguish whether abnormal behavior originates in the motor, transmission, brake, bearing, wheel-ground interface, or external load.

Fault isolation determines which actuator has produced the detected abnormal behavior. In multi-actuator robots, one fault may influence several measured states, causing residuals throughout the system. Structured residuals can be designed so that different actuator faults produce distinct activation patterns. A residual signature matrix then maps combinations of residual responses to candidate faulty actuators or failure modes.

Dedicated observers provide another isolation method. Each observer can be configured to be insensitive to one actuator fault while remaining sensitive to others, or alternatively to monitor a specific actuator independently. Comparing observer residuals enables the diagnostic system to identify the fault location. This approach is particularly useful when a robot has repeated actuator structures such as multiple wheels, legs, joints, thrusters, or distributed drive modules.

Parameter estimation can detect gradual actuator degradation by estimating quantities such as motor torque constant, friction coefficient, transmission efficiency, actuator gain, or time delay. Changes from nominal parameter ranges provide evidence of wear or performance loss before complete failure occurs. Such estimates can support predictive maintenance while simultaneously providing the controller with updated information about remaining actuator capability.

Partial loss of effectiveness is especially important because an actuator may continue responding while delivering only a fraction of the requested output. This condition can remain hidden when motion demand is low but become critical during acceleration, braking, climbing, or disturbance rejection. Fault detection should therefore evaluate actuator capability over relevant operating envelopes rather than assuming that successful low-load motion demonstrates full health.

Actuator saturation must be distinguished from hardware failure. A healthy actuator operating at voltage, current, torque, velocity, or thermal limits cannot follow commands beyond its physical capability. If the controller does not account for these limits, saturation can generate residuals similar to actuator degradation. Diagnostic logic should therefore incorporate commanded limits, available supply voltage, temperature-dependent derating, and operating constraints when interpreting residuals.

Mechanical locking represents a particularly hazardous failure mode in mobile and articulated robots. A locked wheel or joint can generate large reaction forces and alter the system\'s kinematic constraints. Continued command application may increase current, temperature, structural load, or instability. Detection should combine motion inconsistency with current, torque, or force evidence so that the controller can rapidly stop commanding the affected actuator and reconfigure motion.

Intermittent actuator faults are difficult to diagnose because normal behavior may return before conventional persistence thresholds are satisfied. Loose connectors, thermal protection, damaged wiring, communication interruptions, or unstable power electronics can create short recurring failures. Event counters, temporal pattern analysis, health-state memory, and fault recurrence statistics allow the diagnostic system to accumulate evidence across separated abnormal events.

Fault detection thresholds should account for operating conditions. Expected residual magnitudes vary with acceleration, payload, terrain, contact forces, battery voltage, temperature, and robot configuration. Adaptive thresholds can increase tolerance during highly dynamic operation and become more sensitive during stable conditions. This reduces false alarms while preserving the ability to identify small but persistent actuator performance losses.

Multi-actuator consistency provides useful analytical redundancy. In a differential-drive robot, left and right wheel velocities should correspond to commanded vehicle motion and measured yaw rate. In a manipulator, joint torques and accelerations should remain consistent with rigid-body dynamics. In a legged robot, contact forces, joint motion, and body acceleration provide overlapping information that can expose abnormal actuator behavior without identical backup actuators.

Fault isolation must consider coupling between actuators. A malfunctioning joint can change loads on neighboring joints, while one damaged wheel can cause other motors to increase torque to maintain trajectory tracking. These secondary responses should not be mistaken for additional independent faults. Dynamic models and causal reasoning help identify the originating actuator by examining the sequence and physical consistency of residual changes.

Diagnostic confidence is important when fault signatures are ambiguous. Rather than immediately assigning a single failure mode, the system can maintain candidate faults with confidence values based on residual magnitude, temporal persistence, operating context, and cross-sensor consistency. Low-confidence anomalies may trigger enhanced monitoring, while high-confidence faults can initiate actuator exclusion, control reallocation, speed reduction, or a safe-stop procedure.

Once an actuator fault is isolated, the controller must determine the remaining control authority. A redundant robot may continue operating by redistributing commands among healthy actuators. Control allocation can solve for a new set of actuator commands that achieves the required generalized forces while respecting reduced capabilities, saturation limits, thermal constraints, and mechanical restrictions. The achievable motion envelope may become smaller after reconfiguration.

For a mobile robot, loss of one drive or steering actuator may require reduced speed, modified curvature limits, or a restricted route. For a manipulator, a failed joint may reduce reachable workspace or eliminate certain orientations. A legged robot may modify gait and contact strategy. Fault isolation therefore should provide not only the identity of the failed actuator but also an estimate of residual capability useful for motion planning and control.

If sufficient redundancy does not remain, graceful degradation becomes necessary. The system may abandon the original mission and transition toward a minimum-risk condition. Depending on the robot, this may involve stopping on a stable surface, lowering a payload, locking mechanical brakes, reducing joint torque, moving away from people, or entering a controlled shutdown state. Fault diagnosis and safety-state management must therefore operate as coordinated functions.

Actuator health states can be represented as healthy, suspicious, degraded, failed, and recovering. State transitions should combine residual evidence, electrical and mechanical measurements, persistence logic, and self-test information. Recovery from a fault should require sustained evidence of normal behavior because prematurely restoring an unreliable actuator to active control can cause repeated reconfiguration and destabilize the overall system.

Real-time requirements depend strongly on actuator dynamics. High-bandwidth joint, steering, or stabilization actuators may require fault detection within only a few control cycles, while thermal degradation can be evaluated over seconds or minutes. Diagnostic algorithms must therefore be partitioned by criticality and time scale, ensuring that rapidly dangerous failures receive deterministic low-latency responses without sacrificing slower health-monitoring functions.

Verification should include systematic actuator fault injection across the intended operating envelope. Tests can reproduce reduced torque effectiveness, delayed response, stuck actuators, unexpected saturation, intermittent command loss, increased friction, electrical faults, and communication failures. Simulation, software-in-the-loop, hardware-in-the-loop, dynamometer testing, and controlled robot experiments can measure detection latency, isolation accuracy, false alarms, and post-fault stability.

Combined faults should also be considered when their probability or consequence justifies analysis. A single-actuator diagnostic architecture may produce misleading conclusions when multiple actuators degrade simultaneously or when an actuator fault occurs together with a sensor or communication fault. Multiple-hypothesis reasoning, redundancy analysis, and system-level safety supervision can help prevent unsupported isolation decisions under these complex conditions.

Ultimately, actuator fault detection and isolation connects low-level component health with system-level motion safety. By comparing commanded and realized behavior, generating robust residuals, identifying fault signatures, estimating remaining actuator effectiveness, and communicating diagnostic confidence to the controller, the system can respond proportionally to degradation. This enables reconfiguration when control authority remains and a safe transition when continued operation can no longer be guaranteed.

액추에이터 결함 탐지 및 분리(Actuator Fault Detection and Isolation)는 액추에이터가 제어 명령을 실제 힘, 토크, 속도 및 운동으로 직접 변환하기 때문에 로봇의 고장 허용 제어(Fault-Tolerant Control)에서 핵심적인 기능이다. 모터, 구동 전자장치(Drive Electronics), 조향 메커니즘, 브레이크, 유압 요소 또는 로봇 관절에 고장이 발생하면 제어 권한(Control Authority)이 즉각적으로 감소할 수 있다. 따라서 진단 시스템은 비정상적인 구동 상태를 신속하게 탐지하고 어떤 액추에이터 또는 서브시스템이 원인인지 판단해야 한다.

많은 센서 결함과 달리 액추에이터 결함은 내부 측정값뿐만 아니라 로봇의 실제 물리적 응답에도 영향을 미친다. 제어기가 특정 토크나 속도를 명령하더라도 액추에이터는 더 작은 출력, 지연된 출력, 포화된 출력 또는 완전히 다른 응답을 생성할 수 있다. 따라서 탐지는 액추에이터 하드웨어에서 제공되는 전기적 상태 신호에만 의존하지 않고 명령된 동작(Commanded Behavior)과 관측된 시스템 동역학(Observed System Dynamics)을 비교해야 한다.

대표적인 액추에이터 결함 모드(Actuator Fault Mode)에는 완전한 구동 상실, 부분적인 효율 저하, 의도하지 않은 출력, 기계적 잠김(Mechanical Locking), 과도한 마찰, 포화(Saturation), 응답 지연 및 간헐적인 동작이 포함된다. 전기 구동 시스템에서는 권선 결함(Winding Fault), 인버터 결함(Inverter Fault), 엔코더 관련 정류 문제(Commutation Problem), 열적 출력 제한(Thermal Derating), 전원 공급 제한 등이 추가로 발생할 수 있다. 각각의 결함 모드는 제어 가능성(Controllability)에 서로 다른 영향을 미치며 서로 다른 진단 근거가 필요하다.

액추에이터 효율은 u_f = Λu + f_a 관계를 이용하여 유용한 수학적 형태로 표현할 수 있다. 여기서 u는 명령된 액추에이터 입력, u_f는 플랜트(Plant)에 실제로 적용되는 유효 입력, Λ는 승산형 효율 손실(Multiplicative Effectiveness Loss), f_a는 가산형 액추에이터 결함(Additive Actuator Fault)을 나타낸다. 정상 액추에이터의 효율은 공칭값에 가깝지만 계수가 감소하면 부분적인 성능 저하를 나타내고, 0에 가까워지면 심각한 제어 권한 상실을 의미한다.

잔차 생성(Residual Generation)은 액추에이터 결함으로 발생하는 불일치를 식별하는 기본 메커니즘을 제공한다. 제어 명령과 공칭 동역학 모델(Nominal Dynamic Model)을 이용하여 예상되는 로봇 응답을 계산하고, 센서로부터 실제 응답을 획득한다. 이후 가속도, 속도, 위치, 힘, 전류 또는 토크의 차이를 이용해 잔차 신호를 생성할 수 있다. 예상되는 외란으로 설명할 수 없는 지속적인 잔차는 명령된 구동이 올바르게 실현되지 않고 있다는 근거를 제공한다.

모델 기반 관측기(Model-Based Observer)는 액추에이터 결함이 제어 입력을 통해 시스템 동역학에 영향을 주기 때문에 특히 유용하다. 관측기는 명령 입력과 측정 출력을 기반으로 상태 변화를 예측한다. 액추에이터의 효율이 감소하면 실제로 측정된 운동이 예측 궤적(Predicted Trajectory)에서 벗어나기 시작한다. 따라서 액추에이터 제어기 자체가 명령을 정상적으로 실행했다고 보고하는 경우에도 관측기 잔차(Observer Residual)를 통해 비정상적인 구동 상태를 탐지할 수 있다.

미지 입력 관측기(Unknown Input Observer)와 외란 관측기(Disturbance Observer)는 액추에이터 결함과 외부 외란을 구별하는 능력을 향상시킬 수 있다. 이동 로봇이 경사면, 충돌력 또는 증가된 구름 저항(Rolling Resistance)을 경험하면 모터 토크가 감소한 것과 유사한 운동을 나타낼 수 있다. 진단 관측기는 특정 외란을 제거하거나 추정하면서 액추에이터 이상에 대한 민감도를 유지하도록 구성할 수 있으며, 이를 통해 환경적 영향을 하드웨어 결함으로 잘못 분류할 가능성을 줄일 수 있다.

전기적 측정값(Electrical Measurement)은 모터 기반 시스템에서 또 다른 중요한 진단 채널을 제공한다. 명령 토크, 모터 전류, 상전류(Phase Current), 버스 전압(Bus Voltage), 회전자 속도, 온도 및 추정 토크를 비교하여 물리적 일관성을 확인할 수 있다. 높은 전류에도 불구하고 예상보다 낮은 가속도가 발생하면 기계적 저항이나 구동계 손상을 의미할 수 있으며, 큰 토크 명령에도 전류가 낮다면 인버터, 전력단(Power Stage), 통신 또는 모터 제어 고장을 의심할 수 있다.

기계적 정보(Mechanical Information)는 전기적 진단을 보완한다. 관절 위치, 휠 속도, 축 가속도, 로드셀(Load Cell) 측정값, 힘-토크 센서(Force-Torque Sensor), 진동 및 온도는 전기적 측정만으로는 분리하기 어려운 액추에이터 성능 저하를 탐지할 수 있다. 전기적 잔차와 기계적 잔차를 결합하면 비정상 동작의 원인이 모터, 변속기(Transmission), 브레이크, 베어링, 휠-지면 인터페이스(Wheel-Ground Interface), 외부 하중 중 어디에 있는지 구별하는 데 도움이 된다.

결함 분리(Fault Isolation)는 탐지된 비정상 동작을 발생시킨 액추에이터를 결정한다. 다중 액추에이터 로봇(Multi-Actuator Robot)에서는 하나의 결함이 여러 측정 상태에 영향을 주어 시스템 전반에서 잔차를 발생시킬 수 있다. 구조화된 잔차(Structured Residual)는 서로 다른 액추에이터 결함이 구별 가능한 활성화 패턴을 생성하도록 설계할 수 있다. 이후 잔차 시그니처 행렬(Residual Signature Matrix)을 이용하여 잔차 응답의 조합을 결함이 의심되는 액추에이터 또는 고장 모드와 연결한다.

전용 관측기(Dedicated Observer)는 또 다른 결함 분리 방법을 제공한다. 각각의 관측기는 하나의 액추에이터 결함에는 둔감하면서 다른 결함에는 민감하도록 구성하거나, 반대로 특정 액추에이터를 독립적으로 감시하도록 구성할 수 있다. 관측기들의 잔차를 비교하면 진단 시스템이 결함 위치를 식별할 수 있다. 이러한 접근법은 여러 휠, 다리, 관절, 추진기(Thruster) 또는 분산 구동 모듈과 같이 반복적인 액추에이터 구조를 가진 로봇에 특히 유용하다.

파라미터 추정(Parameter Estimation)은 모터 토크 상수(Motor Torque Constant), 마찰 계수(Friction Coefficient), 전달 효율(Transmission Efficiency), 액추에이터 이득(Actuator Gain), 시간 지연 등의 값을 추정하여 점진적인 액추에이터 성능 저하를 탐지할 수 있다. 공칭 파라미터 범위에서 벗어나는 변화는 완전한 고장이 발생하기 전에 마모 또는 성능 저하의 근거를 제공한다. 이러한 추정값은 예지 정비(Predictive Maintenance)를 지원하는 동시에 제어기에 남아 있는 액추에이터 능력에 대한 최신 정보를 제공할 수 있다.

부분적인 효율 손실(Partial Loss of Effectiveness)은 액추에이터가 명령에 계속 반응하면서도 요청된 출력의 일부만 제공할 수 있기 때문에 특히 중요하다. 낮은 운동 요구 조건에서는 이러한 상태가 드러나지 않을 수 있지만 가속, 제동, 경사 등판 또는 외란 억제 상황에서는 치명적인 문제가 될 수 있다. 따라서 결함 탐지는 낮은 부하에서 성공적으로 움직였다는 사실만으로 완전한 정상 상태를 가정하지 않고 관련 운전 영역(Operating Envelope) 전체에서 액추에이터 능력을 평가해야 한다.

액추에이터 포화(Actuator Saturation)는 하드웨어 고장과 구별해야 한다. 정상적인 액추에이터도 전압, 전류, 토크, 속도 또는 열적 한계에서 동작하면 물리적 능력을 넘어서는 명령을 추종할 수 없다. 제어기가 이러한 한계를 고려하지 않으면 포화 상태가 액추에이터 성능 저하와 유사한 잔차를 발생시킬 수 있다. 따라서 진단 로직은 잔차를 해석할 때 명령 제한, 사용 가능한 공급 전압, 온도 의존형 출력 제한(Temperature-Dependent Derating), 운용 제약조건을 함께 고려해야 한다.

기계적 잠김(Mechanical Locking)은 이동 로봇과 관절형 로봇(Articulated Robot)에서 특히 위험한 고장 모드이다. 잠긴 휠이나 관절은 큰 반력을 발생시키고 시스템의 운동학적 제약조건(Kinematic Constraint)을 변화시킬 수 있다. 명령을 계속 적용하면 전류, 온도, 구조 하중 또는 불안정성이 증가할 수 있다. 따라서 탐지 시스템은 운동 불일치와 전류, 토크 또는 힘 정보를 결합하여 제어기가 영향을 받은 액추에이터에 대한 명령을 신속하게 중단하고 운동을 재구성할 수 있도록 해야 한다.

간헐적 액추에이터 결함(Intermittent Actuator Fault)은 일반적인 지속성 임계값(Persistence Threshold)이 충족되기 전에 정상 동작으로 복귀할 수 있어 진단하기 어렵다. 느슨한 커넥터, 열 보호(Thermal Protection), 손상된 배선, 통신 중단 또는 불안정한 전력 전자장치는 짧고 반복적인 고장을 발생시킬 수 있다. 이벤트 카운터(Event Counter), 시간 패턴 분석(Temporal Pattern Analysis), 건전성 상태 메모리(Health-State Memory), 결함 재발 통계를 이용하면 서로 분리되어 발생하는 비정상 이벤트의 근거를 누적할 수 있다.

결함 탐지 임계값(Fault Detection Threshold)은 운전 조건을 고려해야 한다. 예상되는 잔차 크기는 가속도, 탑재 하중, 지형, 접촉력, 배터리 전압, 온도 및 로봇 구성에 따라 변화한다. 적응형 임계값(Adaptive Threshold)은 동적인 운전 상태에서는 허용 범위를 넓히고 안정적인 상태에서는 민감도를 높일 수 있다. 이를 통해 오경보를 줄이면서도 작지만 지속적인 액추에이터 성능 저하를 탐지할 수 있는 능력을 유지한다.

다중 액추에이터 일관성(Multi-Actuator Consistency)은 유용한 해석적 중복성(Analytical Redundancy)을 제공한다. 차동 구동 로봇(Differential-Drive Robot)에서는 좌우 휠 속도가 명령된 차량 운동 및 측정된 요 각속도(Yaw Rate)와 일치해야 한다. 매니퓰레이터(Manipulator)에서는 관절 토크와 가속도가 강체 동역학(Rigid-Body Dynamics)과 일관성을 유지해야 한다. 다족 로봇(Legged Robot)에서는 접촉력, 관절 운동, 몸체 가속도가 서로 중첩되는 정보를 제공하여 동일한 백업 액추에이터 없이도 비정상적인 액추에이터 동작을 탐지할 수 있다.

결함 분리에서는 액추에이터 사이의 결합(Coupling)을 고려해야 한다. 하나의 관절에 고장이 발생하면 인접 관절의 하중이 변화할 수 있으며, 하나의 휠이 손상되면 다른 모터가 궤적 추종을 유지하기 위해 토크를 증가시킬 수 있다. 이러한 이차적 응답(Secondary Response)을 추가적인 독립 결함으로 잘못 판단해서는 안 된다. 동역학 모델과 인과 추론(Causal Reasoning)을 이용하면 잔차 변화의 발생 순서와 물리적 일관성을 분석하여 최초의 결함 액추에이터를 식별하는 데 도움이 된다.

결함 시그니처(Fault Signature)가 모호한 경우에는 진단 신뢰도(Diagnostic Confidence)가 중요하다. 시스템은 하나의 고장 모드를 즉시 확정하기보다 잔차 크기, 시간적 지속성, 운전 상황, 센서 간 일관성에 따라 여러 후보 결함과 각각의 신뢰도를 유지할 수 있다. 낮은 신뢰도의 이상은 강화된 모니터링으로 이어질 수 있으며, 높은 신뢰도의 결함은 액추에이터 제외(Actuator Exclusion), 제어 재할당(Control Reallocation), 속도 감소 또는 안전 정지 절차를 시작할 수 있다.

액추에이터 결함이 분리되면 제어기는 남아 있는 제어 권한(Remaining Control Authority)을 판단해야 한다. 중복성을 가진 로봇은 정상 액추에이터 사이에서 명령을 재분배하여 운전을 계속할 수 있다. 제어 할당(Control Allocation)은 감소된 액추에이터 능력, 포화 한계, 열적 제약조건 및 기계적 제한을 만족하면서 필요한 일반화 힘(Generalized Force)을 생성하는 새로운 액추에이터 명령 집합을 계산할 수 있다. 재구성 이후에는 달성 가능한 운동 영역이 감소할 수 있다.

이동 로봇(Mobile Robot)에서는 하나의 구동 또는 조향 액추에이터가 상실되면 속도를 낮추거나 곡률 제한(Curvature Limit)을 변경하고 제한된 경로를 사용해야 할 수 있다. 매니퓰레이터에서는 하나의 관절 고장이 도달 가능한 작업 공간(Reachable Workspace)을 감소시키거나 특정 자세를 구현할 수 없게 만들 수 있다. 다족 로봇은 보행 패턴(Gait)과 접촉 전략을 변경할 수 있다. 따라서 결함 분리는 고장 난 액추에이터의 식별뿐만 아니라 운동 계획 및 제어에 사용할 수 있는 잔존 능력(Residual Capability)의 추정값도 제공해야 한다.

충분한 중복성이 남아 있지 않다면 점진적 성능 저하(Graceful Degradation)가 필요하다. 시스템은 기존 임무를 포기하고 최소 위험 상태(Minimum-Risk Condition)로 전환할 수 있다. 로봇의 종류에 따라 안정적인 지면에서 정지하거나, 탑재물을 내려놓고, 기계식 브레이크를 잠그거나, 관절 토크를 감소시키고, 사람으로부터 멀어지거나, 제어된 종료(Controlled Shutdown) 상태로 진입할 수 있다. 따라서 결함 진단과 안전 상태 관리(Safety-State Management)는 상호 조정된 기능으로 동작해야 한다.

액추에이터 건전성 상태(Actuator Health State)는 정상(Healthy), 의심(Suspicious), 성능 저하(Degraded), 고장(Failed), 복구 중(Recovering)으로 표현할 수 있다. 상태 전이는 잔차 근거, 전기적 및 기계적 측정값, 지속성 논리, 자체 진단(Self-Test) 정보를 결합하여 결정해야 한다. 신뢰할 수 없는 액추에이터를 너무 빨리 능동 제어에 복귀시키면 반복적인 재구성이 발생하고 전체 시스템이 불안정해질 수 있으므로 결함 복구는 정상 동작이 지속적으로 확인된 이후에 이루어져야 한다.

실시간 요구사항(Real-Time Requirement)은 액추에이터 동역학에 크게 의존한다. 고대역폭 관절, 조향 또는 안정화 액추에이터는 몇 번의 제어 주기 내에서 결함을 탐지해야 할 수 있지만 열적 성능 저하는 수초 또는 수분 동안 평가할 수 있다. 따라서 진단 알고리즘은 중요도와 시간 척도(Time Scale)에 따라 구분되어야 하며, 빠르게 위험해지는 고장에 결정론적이고 낮은 지연시간의 대응을 제공하면서 느린 건전성 모니터링 기능도 유지해야 한다.

검증(Verification)은 의도된 운전 영역 전반에서 체계적인 액추에이터 결함 주입(Actuator Fault Injection)을 포함해야 한다. 시험에서는 토크 효율 감소, 응답 지연, 액추에이터 고착(Stuck Actuator), 예상하지 못한 포화, 간헐적인 명령 손실, 마찰 증가, 전기적 결함 및 통신 고장을 재현할 수 있다. 시뮬레이션(Simulation), 소프트웨어 인 더 루프(Software-in-the-Loop, SIL), 하드웨어 인 더 루프(Hardware-in-the-Loop, HIL), 동력계 시험(Dynamometer Testing), 통제된 로봇 실험을 통해 탐지 지연시간, 결함 분리 정확도, 오경보 및 고장 이후 안정성을 측정할 수 있다.

복합 결함(Combined Fault)은 발생 확률이나 결과의 심각성이 분석을 정당화하는 경우 함께 고려해야 한다. 단일 액추에이터를 가정한 진단 아키텍처는 여러 액추에이터가 동시에 성능 저하를 일으키거나 액추에이터 결함과 센서 또는 통신 결함이 동시에 발생하면 잘못된 결론을 생성할 수 있다. 다중 가설 추론(Multiple-Hypothesis Reasoning), 중복성 분석(Redundancy Analysis), 시스템 수준 안전 감독(System-Level Safety Supervision)을 활용하면 이러한 복잡한 조건에서 근거가 부족한 결함 분리 결정을 방지할 수 있다.

궁극적으로 액추에이터 결함 탐지 및 분리(Actuator Fault Detection and Isolation)는 저수준 구성요소 건전성(Component Health)과 시스템 수준 운동 안전성(Motion Safety)을 연결한다. 명령된 동작과 실제 구현된 동작을 비교하고, 강인한 잔차를 생성하며, 결함 시그니처를 식별하고, 남아 있는 액추에이터 효율을 추정하여 진단 신뢰도를 제어기에 전달함으로써 시스템은 성능 저하 수준에 비례하여 대응할 수 있다. 이를 통해 제어 권한이 남아 있는 경우에는 재구성을 수행하고, 지속적인 운전을 더 이상 보장할 수 없는 경우에는 안전한 상태로 전환할 수 있다.

##  

## 08.04 Redundant Sensor Voter Algorithm [w/Code]

![](images/image4.png){width="7.268055555555556in" height="7.268055555555556in"}

Redundant sensor voter algorithms provide fault tolerance by combining measurements from multiple sensors that observe the same or closely related physical quantity. Instead of allowing a single sensor to determine the feedback signal, the voter evaluates consistency among redundant measurements and selects or synthesizes a trusted output. This architecture is widely applicable to position, velocity, attitude, force, temperature, range, and other safety-relevant measurements.

The fundamental assumption is that independent sensor faults are less likely to affect all redundant channels simultaneously. If three sensors measure the same variable and one produces an abnormal value, the two mutually consistent measurements can provide evidence about the correct state. This principle enables the system to detect an outlier and continue operating without immediately losing the measured quantity, provided that sufficient healthy redundancy remains.

Triple modular redundancy, or TMR, is a classical voting structure using three independent measurement channels. For scalar measurements x1, x2, and x3, a median voter can produce x_v = median(x1, x2, x3). When one sensor deviates substantially while the other two remain consistent, the median naturally rejects the extreme value. This method is computationally simple and does not require explicit identification of the faulty channel before producing a usable output.

Majority voting is suitable when sensor outputs are discrete states rather than continuous measurements. If redundant channels report values such as active/inactive, locked/unlocked, or valid/invalid, the state reported by the majority can be selected. Majority voting is easy to verify, but its reliability depends on independence assumptions and the number of simultaneous faults that the redundant architecture is designed to tolerate.

Continuous-valued sensors require additional consistency logic because their outputs rarely match exactly. Pairwise differences such as d12 = \|x1 − x2\|, d13 = \|x1 − x3\|, and d23 = \|x2 − x3\| can be compared with predefined tolerances. If two measurements remain mutually consistent while the third differs significantly, the inconsistent channel can be marked as suspicious and excluded or assigned reduced confidence.

A simple average voter computes the mean of available sensor measurements and can reduce independent zero-mean noise when all sensors are healthy. However, ordinary averaging is vulnerable to large biases or extreme outliers because one faulty measurement can significantly distort the fused result. Average voting is therefore most appropriate when fault detection has already validated the channels or when additional robust weighting limits the influence of abnormal measurements.

Weighted voting improves fusion by assigning each sensor a weight according to accuracy, uncertainty, health, or operating condition. A fused value can be expressed as x_v = Σw_i x_i / Σw_i. High-confidence sensors receive greater influence, while degraded channels receive smaller weights. Weights may be fixed from calibration data or dynamically updated using residual statistics, estimated covariance, environmental conditions, and diagnostic health indicators.

Median voting is more robust to isolated outliers than simple averaging. With an odd number of redundant scalar measurements, sorting the measurements and selecting the middle value prevents one extreme sensor from dominating the result. The method is particularly attractive for embedded systems because it is deterministic and inexpensive. However, it does not fully exploit differences in sensor precision and becomes less effective when multiple channels fail coherently.

Mid-value selection can also be extended with averaging. A voter may first reject measurements outside an acceptable consistency region and then average the remaining healthy channels. This combines the robustness of outlier rejection with the noise-reduction advantage of averaging. Such hybrid voting is useful when sensors have similar characteristics but occasional biases, spikes, communication corruption, or intermittent failures must be tolerated.

Threshold selection is central to voter performance. If the allowable disagreement threshold is too narrow, normal measurement noise and calibration differences can cause healthy sensors to be rejected. If it is too wide, significant sensor faults may remain undetected. Thresholds should therefore reflect measurement accuracy, expected noise, dynamic response, synchronization error, environmental sensitivity, and the consequences of accepting an incorrect measurement.

Adaptive voting thresholds can improve performance across changing operating conditions. Sensor disagreement may naturally increase during rapid acceleration, vibration, high angular velocity, temperature transitions, or difficult environmental conditions. A voter can modify its tolerance using operating state, estimated covariance, signal quality, or dynamic model uncertainty. This allows high sensitivity during stable operation without excessive false rejection during demanding maneuvers.

Time synchronization is essential when comparing redundant sensor measurements. Two healthy sensors sampled at different times can appear inconsistent when the measured state changes rapidly. Hardware triggering, synchronized clocks, timestamp alignment, interpolation, or prediction to a common reference time can reduce this problem. Voting logic should distinguish true measurement disagreement from errors caused by latency, jitter, or asynchronous data acquisition.

Sensor diversity can improve protection against common-mode failures. Redundancy based on identical sensors provides straightforward comparison but may expose all channels to the same design defect, environmental limitation, or software error. Diverse redundancy combines different sensing principles, such as wheel odometry, IMU, vision, LiDAR, and GNSS, so that a single physical phenomenon is less likely to corrupt every measurement source in the same manner.

Voting among heterogeneous sensors requires transformation into a common representation. Sensors may measure different quantities, coordinate frames, bandwidths, or noise characteristics. State estimation and physical models can convert these measurements into comparable predictions, such as velocity, orientation, or position. The voter then evaluates consistency at the state or residual level rather than directly comparing incompatible raw sensor outputs.

Residual-based voting integrates naturally with fault detection. For each sensor, a residual can be calculated against a reference estimate, dynamic model, or consensus measurement. Sensors with small residuals receive high confidence, while channels with persistent large residuals are downgraded. This approach allows the voter to combine redundancy with model-based reasoning rather than relying only on agreement between sensors.

A critical challenge occurs when two sensors disagree and no third independent reference exists. With only dual redundancy, disagreement identifies inconsistency but cannot determine which sensor is correct without additional information. A model prediction, another sensing modality, physical constraint, or historical health information is required for isolation. Triple or higher redundancy therefore provides stronger voting capability but increases cost and system complexity.

Voter algorithms should maintain sensor health states over time. A channel may transition through healthy, suspicious, degraded, failed, and recovering states according to consistency tests and diagnostic evidence. Persistence logic prevents a single transient disagreement from immediately removing a sensor. Likewise, recovery should require sustained agreement before a previously faulty channel is allowed to regain significant influence over the fused measurement.

Hysteresis is useful for preventing unstable switching near decision thresholds. A sensor may require a relatively strong inconsistency to transition from healthy to degraded, while a stricter period of sustained consistency may be required for recovery. Separate entry and exit criteria prevent measurement noise from repeatedly enabling and disabling a channel, which could otherwise introduce discontinuities into state estimation and feedback control.

Confidence-based voting provides a continuous alternative to hard inclusion or exclusion. Each sensor is assigned a confidence value between low and high reliability, and the fused output changes gradually as confidence evolves. This is useful for sensors whose performance deteriorates progressively rather than failing abruptly. Confidence can incorporate residual magnitude, noise variance, signal quality, temperature, communication health, and environmental suitability.

The voter should also detect frozen or stale measurements. A sensor that repeatedly transmits the same numerically valid value may pass simple range checks while no longer representing the changing physical state. Timestamp monitoring, rate-of-change checks, sequence counters, and comparison with predicted dynamics help identify stale data. This is especially important in networked robots where communication failures may preserve the last valid measurement.

Out-of-range and plausibility checks provide an initial diagnostic layer before voting. Measurements violating physical limits, impossible rates of change, invalid status flags, or known sensor constraints can be rejected immediately. These checks reduce the probability that obviously corrupted data influences consensus calculations. More subtle faults that remain physically plausible must then be detected through redundancy, temporal consistency, or model-based residuals.

Voting algorithms must consider correlated errors. Redundant sensors mounted on the same structure may experience identical vibration, thermal gradients, electromagnetic interference, or mechanical deformation. Their measurements can agree closely even while all are biased. Common power supplies, clocks, buses, calibration procedures, and software drivers create additional shared dependencies. Agreement alone should therefore not be interpreted as absolute evidence of correctness.

The fused voter output should include quality information in addition to the measurement value. Useful metadata can include the number of participating sensors, rejected channels, estimated uncertainty, confidence level, diagnostic state, and degradation flags. Downstream state estimators and controllers can use this information to modify covariance, reduce motion limits, or trigger additional safety actions when redundancy has been reduced.

Loss of redundancy is itself a significant system event. A three-sensor architecture may continue producing a valid output after one channel fails, but it no longer possesses the same ability to tolerate another fault. The system should therefore distinguish between measurement availability and redundancy availability. Continued operation may be permitted while speed, mission duration, environmental exposure, or other operational limits are reduced.

Voter design must avoid abrupt output discontinuities during sensor switching. Selecting a new channel with a different calibration offset can introduce a step into the feedback signal and cause undesirable control action. Bumpless transfer techniques, blending, offset compensation, filtering, or estimator-based transitions can provide continuity when sensors are excluded or restored. The transition dynamics should remain compatible with the control-loop bandwidth.

Real-time implementation should be deterministic and computationally bounded. Voting may execute at high rates for IMUs, encoders, joint sensors, or flight-control measurements. The algorithm must handle missing packets, invalid timestamps, delayed channels, numerical exceptions, and changing sensor membership without blocking the control loop. Defined execution order and bounded memory use are particularly important for safety-critical embedded implementations.

Verification should include nominal operation as well as systematic sensor fault injection. Tests can introduce biases, drift, noise increases, frozen outputs, spikes, timing offsets, packet loss, gain changes, and simultaneous faults. Evaluation should measure false rejection, missed detection, isolation accuracy, voter output error, switching transients, recovery behavior, and the ability to preserve stable closed-loop control after redundancy is reduced.

Common-mode scenarios should be tested separately because conventional single-fault voting may not expose them. Examples include multiple sensors sharing an incorrect calibration, simultaneous communication corruption, environmental interference affecting identical devices, or a common software conversion error. Diverse references and analytical checks should demonstrate that the architecture does not rely exclusively on majority agreement when the majority itself may be wrong.

The redundant sensor voter ultimately acts as a trusted interface between raw sensing and state estimation or control. By evaluating agreement, uncertainty, health, timing, and physical plausibility, it transforms multiple potentially imperfect measurements into a more dependable representation of system state. Effective voting does not merely choose the majority; it manages sensor confidence and redundancy so that safe operation can continue while sufficient trustworthy information remains.

중복 센서 보터 알고리즘(Redundant Sensor Voter Algorithm)은 동일하거나 밀접하게 관련된 물리량을 관측하는 여러 센서의 측정값을 결합하여 고장 허용성(Fault Tolerance)을 제공한다. 하나의 센서가 피드백 신호를 단독으로 결정하도록 하는 대신, 보터(Voter)는 중복 측정값 사이의 일관성을 평가하고 신뢰할 수 있는 출력을 선택하거나 합성한다. 이러한 아키텍처는 위치, 속도, 자세, 힘, 온도, 거리 및 기타 안전 관련 측정값에 폭넓게 적용할 수 있다.

기본적인 가정은 독립적인 센서 결함이 모든 중복 채널에 동시에 영향을 미칠 가능성이 낮다는 것이다. 세 개의 센서가 동일한 변수를 측정하고 하나의 센서가 비정상적인 값을 생성하면 서로 일치하는 나머지 두 측정값이 올바른 상태에 대한 근거를 제공할 수 있다. 이 원리를 이용하면 충분한 정상 중복성이 남아 있는 경우 이상값(Outlier)을 탐지하고 측정 대상 물리량을 즉시 상실하지 않으면서 운전을 지속할 수 있다.

삼중 모듈 중복(Triple Modular Redundancy, TMR)은 세 개의 독립적인 측정 채널을 사용하는 대표적인 보팅 구조(Voting Structure)이다. 스칼라 측정값 x1, x2, x3에 대해 중앙값 보터(Median Voter)는 x_v = median(x1, x2, x3)를 출력할 수 있다. 하나의 센서가 크게 벗어나고 나머지 두 센서가 서로 일치하면 중앙값은 자연스럽게 극단적인 값을 제거한다. 이 방법은 계산이 단순하며 사용 가능한 출력을 생성하기 전에 결함 채널을 명시적으로 식별할 필요가 없다.

다수결 보팅(Majority Voting)은 센서 출력이 연속적인 측정값이 아니라 이산 상태(Discrete State)인 경우 적합하다. 중복 채널이 활성/비활성, 잠금/해제 또는 유효/무효와 같은 값을 보고한다면 다수의 채널이 보고한 상태를 선택할 수 있다. 다수결 보팅은 검증하기 쉽지만 신뢰성은 채널 간 독립성 가정과 중복 아키텍처가 동시에 허용하도록 설계된 결함 개수에 따라 달라진다.

연속값 센서(Continuous-Valued Sensor)는 출력값이 정확하게 일치하는 경우가 거의 없으므로 추가적인 일관성 판단 로직이 필요하다. d12 = \|x1 − x2\|, d13 = \|x1 − x3\|, d23 = \|x2 − x3\|와 같은 쌍별 차이(Pairwise Difference)를 사전에 정의된 허용 오차와 비교할 수 있다. 두 측정값이 서로 일관성을 유지하고 세 번째 값만 크게 차이가 난다면 해당 채널을 의심 상태로 표시하고 제외하거나 신뢰도를 낮출 수 있다.

단순 평균 보터(Simple Average Voter)는 사용 가능한 센서 측정값의 평균을 계산하며 모든 센서가 정상인 경우 독립적인 영평균 노이즈(Zero-Mean Noise)를 감소시킬 수 있다. 그러나 일반적인 평균은 큰 편향이나 극단적인 이상값에 취약하여 하나의 결함 측정값이 융합 결과를 크게 왜곡할 수 있다. 따라서 평균 보팅은 결함 탐지를 통해 채널이 이미 검증되었거나 추가적인 강인 가중치(Robust Weighting)를 이용하여 비정상 측정값의 영향을 제한하는 경우에 적합하다.

가중 보팅(Weighted Voting)은 정확도, 불확실성, 건전성 또는 운전 조건에 따라 각 센서에 가중치를 부여하여 융합 성능을 향상시킨다. 융합값은 x_v = Σw_i x_i / Σw_i로 표현할 수 있다. 신뢰도가 높은 센서는 더 큰 영향력을 가지며 성능이 저하된 채널에는 더 작은 가중치가 적용된다. 가중치는 교정 데이터(Calibration Data)를 기반으로 고정하거나 잔차 통계, 추정 공분산(Estimated Covariance), 환경 조건 및 진단 건전성 지표에 따라 동적으로 갱신할 수 있다.

중앙값 보팅(Median Voting)은 단순 평균보다 개별 이상값에 강인하다. 홀수 개의 중복 스칼라 측정값이 있는 경우 측정값을 정렬하고 가운데 값을 선택하면 하나의 극단적인 센서가 결과를 지배하는 것을 방지할 수 있다. 이 방법은 결정론적이고 계산 비용이 낮기 때문에 임베디드 시스템(Embedded System)에 특히 적합하다. 그러나 센서별 정밀도 차이를 충분히 활용하지 못하며 여러 채널이 동일한 방향으로 고장 나는 경우에는 효과가 감소한다.

중간값 선택(Mid-Value Selection)은 평균 연산과 결합하여 확장할 수도 있다. 보터는 먼저 허용 가능한 일관성 영역 밖의 측정값을 제거하고 남아 있는 정상 채널의 평균을 계산할 수 있다. 이를 통해 이상값 제거(Outlier Rejection)의 강인성과 평균화의 노이즈 감소 효과를 결합할 수 있다. 이러한 하이브리드 보팅(Hybrid Voting)은 유사한 특성의 센서에서 간헐적인 편향, 스파이크(Spike), 통신 데이터 손상 또는 간헐적 결함을 허용해야 하는 경우 유용하다.

임계값 선택(Threshold Selection)은 보터 성능을 결정하는 핵심 요소이다. 허용되는 불일치 임계값이 너무 좁으면 정상적인 측정 노이즈와 교정 차이로 인해 정상 센서가 제거될 수 있다. 반대로 임계값이 너무 넓으면 상당한 센서 결함이 탐지되지 않을 수 있다. 따라서 임계값은 측정 정확도, 예상 노이즈, 동적 응답, 동기화 오차, 환경 민감도 및 잘못된 측정값을 허용했을 때의 결과를 반영해야 한다.

적응형 보팅 임계값(Adaptive Voting Threshold)은 변화하는 운전 조건 전반에서 성능을 향상시킬 수 있다. 급가속, 진동, 높은 각속도, 온도 변화 또는 열악한 환경 조건에서는 센서 간 불일치가 자연스럽게 증가할 수 있다. 보터는 운전 상태, 추정 공분산, 신호 품질 또는 동역학 모델 불확실성을 이용하여 허용 오차를 변경할 수 있다. 이를 통해 안정적인 운전에서는 높은 탐지 민감도를 유지하면서도 격렬한 기동에서는 과도한 오제거(False Rejection)를 방지할 수 있다.

중복 센서 측정값을 비교할 때 시간 동기화(Time Synchronization)는 필수적이다. 정상적인 두 센서도 서로 다른 시점에 샘플링되면 측정 대상 상태가 빠르게 변화하는 동안 서로 불일치하는 것처럼 보일 수 있다. 하드웨어 트리거(Hardware Trigger), 동기화된 클록(Synchronized Clock), 타임스탬프 정렬(Timestamp Alignment), 보간(Interpolation) 또는 공통 기준 시점으로의 예측을 통해 이러한 문제를 줄일 수 있다. 보팅 로직은 실제 측정 불일치와 지연시간, 지터 또는 비동기 데이터 획득으로 발생하는 오차를 구분해야 한다.

센서 다양성(Sensor Diversity)은 공통 원인 고장(Common-Mode Failure)에 대한 보호 능력을 향상시킬 수 있다. 동일한 센서에 기반한 중복성은 직접적인 비교가 쉽지만 동일한 설계 결함, 환경적 한계 또는 소프트웨어 오류가 모든 채널에 영향을 미칠 수 있다. 이종 중복성(Diverse Redundancy)은 휠 오도메트리(Wheel Odometry), 관성 측정 장치(IMU), 비전(Vision), 라이다(LiDAR), 위성항법시스템(GNSS)과 같이 서로 다른 센싱 원리를 결합하여 하나의 물리적 현상이 모든 측정원에 동일한 방식으로 영향을 줄 가능성을 낮춘다.

이종 센서(Heterogeneous Sensor) 사이의 보팅을 위해서는 측정값을 공통 표현(Common Representation)으로 변환해야 한다. 센서들은 서로 다른 물리량, 좌표계, 대역폭 또는 노이즈 특성을 측정할 수 있다. 상태 추정(State Estimation)과 물리 모델을 이용하면 이러한 측정값을 속도, 자세 또는 위치와 같이 비교 가능한 예측값으로 변환할 수 있다. 이후 보터는 서로 호환되지 않는 원시 센서 출력 자체를 직접 비교하기보다 상태 또는 잔차 수준에서 일관성을 평가한다.

잔차 기반 보팅(Residual-Based Voting)은 결함 탐지와 자연스럽게 통합될 수 있다. 각 센서에 대해 기준 추정값, 동역학 모델 또는 합의 측정값(Consensus Measurement)과 비교하여 잔차를 계산할 수 있다. 작은 잔차를 가진 센서에는 높은 신뢰도를 부여하고 지속적으로 큰 잔차를 보이는 채널의 신뢰도는 낮춘다. 이러한 접근법을 이용하면 센서 간 단순 일치 여부에만 의존하지 않고 중복성과 모델 기반 추론(Model-Based Reasoning)을 결합할 수 있다.

두 센서가 서로 불일치하지만 세 번째 독립 기준이 존재하지 않는 경우에는 중요한 문제가 발생한다. 이중 중복(Dual Redundancy)에서는 불일치가 존재한다는 사실은 식별할 수 있지만 추가적인 정보가 없으면 어느 센서가 올바른지 판단할 수 없다. 결함 분리(Fault Isolation)를 위해서는 모델 예측, 다른 센싱 방식, 물리적 제약조건 또는 과거 건전성 정보가 필요하다. 따라서 삼중 또는 그 이상의 중복성은 더 강력한 보팅 능력을 제공하지만 비용과 시스템 복잡성을 증가시킨다.

보터 알고리즘은 시간에 따른 센서 건전성 상태(Sensor Health State)를 유지해야 한다. 채널은 일관성 검사와 진단 근거에 따라 정상(Healthy), 의심(Suspicious), 성능 저하(Degraded), 고장(Failed), 복구 중(Recovering) 상태를 거칠 수 있다. 지속성 논리(Persistence Logic)는 단일 순간의 불일치로 인해 센서가 즉시 제거되는 것을 방지한다. 마찬가지로 이전에 결함이 발생했던 채널이 융합 측정값에 다시 큰 영향을 주기 전에 일정 시간 동안 지속적인 일치 상태가 확인되어야 한다.

히스테리시스(Hysteresis)는 판단 임계값 부근에서 불안정한 전환을 방지하는 데 유용하다. 센서가 정상 상태에서 성능 저하 상태로 전환되기 위해서는 비교적 강한 불일치가 필요하도록 설정할 수 있으며, 복구를 위해서는 더욱 엄격하고 지속적인 일치 조건을 요구할 수 있다. 서로 다른 진입 및 이탈 기준(Entry and Exit Criteria)을 적용하면 측정 노이즈로 인해 채널이 반복적으로 활성화되고 비활성화되는 것을 방지할 수 있으며, 이는 상태 추정과 피드백 제어에서 발생할 수 있는 불연속성을 감소시킨다.

신뢰도 기반 보팅(Confidence-Based Voting)은 채널을 완전히 포함하거나 제외하는 방식에 대한 연속적인 대안을 제공한다. 각 센서에는 낮은 신뢰도에서 높은 신뢰도까지의 값을 할당하고 신뢰도가 변화함에 따라 융합 출력도 점진적으로 변화한다. 이러한 방식은 갑자기 고장 나기보다 성능이 점진적으로 저하되는 센서에 유용하다. 신뢰도에는 잔차 크기, 노이즈 분산, 신호 품질, 온도, 통신 건전성 및 환경 적합성을 반영할 수 있다.

보터는 고정되거나 오래된 측정값(Frozen or Stale Measurement)도 탐지해야 한다. 수치적으로 유효한 동일한 값을 반복적으로 전송하는 센서는 단순 범위 검사를 통과할 수 있지만 변화하는 실제 물리 상태를 더 이상 나타내지 못할 수 있다. 타임스탬프 모니터링, 변화율 검사(Rate-of-Change Check), 시퀀스 카운터(Sequence Counter), 예측 동역학과의 비교를 통해 오래된 데이터를 식별할 수 있다. 이는 통신 고장으로 마지막 정상 측정값이 계속 유지될 수 있는 네트워크 기반 로봇에서 특히 중요하다.

범위 초과 및 타당성 검사(Out-of-Range and Plausibility Check)는 보팅 이전에 수행되는 초기 진단 계층을 제공한다. 물리적 한계, 불가능한 변화율, 유효하지 않은 상태 플래그 또는 알려진 센서 제약조건을 위반하는 측정값은 즉시 제거할 수 있다. 이러한 검사를 통해 명백하게 손상된 데이터가 합의 계산(Consensus Calculation)에 영향을 미칠 가능성을 줄일 수 있다. 물리적으로 타당한 범위 안에 남아 있는 미세한 결함은 중복성, 시간적 일관성 또는 모델 기반 잔차를 통해 추가적으로 탐지해야 한다.

보팅 알고리즘은 상관된 오류(Correlated Error)를 고려해야 한다. 동일한 구조물에 장착된 중복 센서는 같은 진동, 온도 구배(Thermal Gradient), 전자기 간섭(Electromagnetic Interference), 기계적 변형의 영향을 받을 수 있다. 이 경우 모든 센서에 편향이 발생하면서도 측정값들은 서로 잘 일치할 수 있다. 공통 전원, 클록, 버스, 교정 절차 및 소프트웨어 드라이버 역시 추가적인 공유 의존성을 생성한다. 따라서 센서 간 일치만을 측정값 정확성에 대한 절대적인 근거로 해석해서는 안 된다.

융합된 보터 출력(Fused Voter Output)은 측정값뿐만 아니라 품질 정보(Quality Information)도 포함해야 한다. 유용한 메타데이터에는 참여 중인 센서 수, 제거된 채널, 추정 불확실성, 신뢰도 수준, 진단 상태 및 성능 저하 플래그가 포함될 수 있다. 후단의 상태 추정기(State Estimator)와 제어기(Controller)는 이러한 정보를 이용하여 공분산을 조정하고 운동 제한을 낮추거나 중복성이 감소했을 때 추가적인 안전 조치를 시작할 수 있다.

중복성 상실(Loss of Redundancy)은 그 자체로 중요한 시스템 이벤트이다. 세 개의 센서를 사용하는 아키텍처는 하나의 채널이 고장 난 이후에도 유효한 출력을 계속 생성할 수 있지만 추가적인 결함을 허용할 수 있는 능력은 이전과 같지 않다. 따라서 시스템은 측정값 가용성(Measurement Availability)과 중복성 가용성(Redundancy Availability)을 구분해야 한다. 운전을 계속할 수 있더라도 속도, 임무 지속시간, 환경 노출 또는 기타 운용 한계를 줄여야 할 수 있다.

보터 설계는 센서 전환 과정에서 갑작스러운 출력 불연속(Output Discontinuity)을 방지해야 한다. 교정 오프셋이 다른 새로운 채널을 선택하면 피드백 신호에 계단형 변화가 발생하여 바람직하지 않은 제어 동작을 유발할 수 있다. 무충격 전환(Bumpless Transfer), 블렌딩(Blending), 오프셋 보상(Offset Compensation), 필터링 또는 추정기 기반 전환(Estimator-Based Transition)을 이용하면 센서가 제외되거나 복구될 때 연속성을 확보할 수 있다. 전환 동역학은 제어 루프 대역폭(Control-Loop Bandwidth)과 호환되어야 한다.

실시간 구현(Real-Time Implementation)은 결정론적이며 계산 시간이 제한되어야 한다. 보팅은 관성 측정 장치, 엔코더, 관절 센서 또는 비행 제어 측정값과 같이 높은 주파수로 실행될 수 있다. 알고리즘은 제어 루프를 차단하지 않으면서 누락된 패킷, 잘못된 타임스탬프, 지연된 채널, 수치 예외(Numerical Exception), 변화하는 센서 구성원을 처리할 수 있어야 한다. 정의된 실행 순서와 제한된 메모리 사용은 안전 중요 임베디드 구현에서 특히 중요하다.

검증(Verification)은 정상 운전뿐만 아니라 체계적인 센서 결함 주입(Sensor Fault Injection)을 포함해야 한다. 시험에서는 편향, 드리프트, 노이즈 증가, 출력 고정, 스파이크, 시간 오프셋, 패킷 손실, 이득 변화 및 동시 결함을 주입할 수 있다. 평가 항목에는 정상 센서 오제거(False Rejection), 미탐지(Missed Detection), 결함 분리 정확도, 보터 출력 오차, 전환 과도응답(Switching Transient), 복구 동작 및 중복성이 감소한 이후에도 안정적인 폐루프 제어를 유지할 수 있는지가 포함되어야 한다.

공통 원인 고장(Common-Mode Failure) 시나리오는 기존의 단일 결함 보팅으로 탐지되지 않을 수 있으므로 별도로 시험해야 한다. 대표적인 사례에는 여러 센서가 동일하게 잘못 교정된 경우, 동시 통신 데이터 손상, 동일 장치에 영향을 미치는 환경 간섭 또는 공통 소프트웨어 변환 오류가 있다. 다양한 독립 기준(Diverse Reference)과 해석적 검사를 이용하여 다수의 센서 자체가 잘못될 수 있는 상황에서도 아키텍처가 단순한 다수결 일치에만 의존하지 않는다는 것을 입증해야 한다.

궁극적으로 중복 센서 보터(Redundant Sensor Voter)는 원시 센싱(Raw Sensing)과 상태 추정 또는 제어 사이에서 신뢰할 수 있는 인터페이스 역할을 수행한다. 측정값의 일치도, 불확실성, 건전성, 시간 정보 및 물리적 타당성을 평가함으로써 잠재적으로 불완전한 여러 측정값을 더욱 신뢰할 수 있는 시스템 상태 표현으로 변환한다. 효과적인 보팅은 단순히 다수의 값을 선택하는 것이 아니라 센서 신뢰도와 중복성을 관리하여 충분히 신뢰할 수 있는 정보가 남아 있는 동안 안전한 운전을 지속할 수 있도록 한다.

##  

## 08.05 Manipulator Reconfiguration on Joint Failure [w/Code]

![](images/image5.png){width="7.268055555555556in" height="7.268055555555556in"}

Manipulator reconfiguration on joint failure is a fault-tolerant control strategy that allows a robotic arm to preserve safe and useful motion after one or more joints lose normal functionality. Because each joint contributes to the manipulator's position, orientation, force capability, and reachable workspace, a failure can change the robot's kinematic and dynamic properties immediately. Reconfiguration adapts control objectives to the capabilities that remain.

Joint failures can occur in actuators, transmissions, brakes, encoders, motor drives, power electronics, communication interfaces, or mechanical structures. Their effects range from reduced torque and increased friction to uncontrolled motion, position-sensing loss, or complete locking. Effective reconfiguration therefore begins with fault detection and isolation that determines not only which joint is affected but also the physical behavior imposed by the failure.

A joint failure is commonly classified according to the remaining degree of control authority. A free-swinging joint may lose active torque while retaining passive motion, whereas a locked joint becomes fixed at a particular angle. A partially degraded joint may still generate limited torque or velocity. These cases produce different post-fault kinematics, so the controller must represent the failed joint condition explicitly rather than simply disabling its command.

For an n-degree-of-freedom manipulator, the nominal joint vector q contains all active coordinates. If joint j becomes locked at q_j = q_j,f, that coordinate becomes a fixed parameter and the effective number of controllable degrees of freedom decreases. The forward kinematic model must then be recalculated using the remaining active joints. This produces a reduced configuration space and a new mapping between joint motion and end-effector motion.

The manipulator Jacobian is central to post-fault reconfiguration. Under normal operation, end-effector velocity can be represented by ẋ = J(q)q̇. When a joint becomes unavailable, the corresponding Jacobian column can no longer contribute freely to commanded motion. A reduced Jacobian J_r is formed from the healthy controllable joints, and feasible Cartesian velocity commands must lie within the range space of this reduced mapping.

Loss of a joint does not always imply complete loss of the task. A redundant manipulator may have more joints than required for a particular end-effector objective. For example, a seven-degree-of-freedom arm performing a six-dimensional pose task may retain substantial capability after one joint is immobilized, depending on configuration. Reconfiguration exploits this kinematic redundancy to preserve the highest-priority task whenever sufficient independent motion directions remain.

The reachable workspace changes after joint failure and should be evaluated explicitly. Some Cartesian positions may remain reachable while particular orientations become impossible, and other regions may disappear entirely from the feasible workspace. A post-fault controller should avoid commanding unreachable targets because persistent attempts to achieve infeasible poses can cause saturation, excessive internal forces, unstable optimization, or unnecessary stress on healthy joints.

Task prioritization becomes essential when full motion capability cannot be maintained. Safety-critical objectives such as collision avoidance, payload stabilization, maintaining support, or moving away from a person should receive higher priority than exact trajectory tracking. Lower-priority objectives such as preferred posture, energy optimization, or precise orientation can be relaxed when the remaining degrees of freedom are insufficient to satisfy every requirement simultaneously.

Task-space reconfiguration can modify the commanded end-effector objective according to the available mobility. If full six-dimensional position and orientation control is no longer possible, the controller may preserve three-dimensional position while relaxing one or more orientation axes. Alternatively, maintaining tool orientation may be more important than exact position for some processes. The selected reduced task should reflect mission requirements and safety consequences.

Inverse kinematics must be reformulated after a joint failure. A conventional inverse solution that assumes all joints are controllable may repeatedly command the failed coordinate or generate infeasible joint velocities. Fault-aware inverse kinematics removes or constrains the affected joint and solves for the remaining variables. Pseudoinverse, damped least-squares, constrained optimization, or quadratic programming methods can calculate feasible post-fault motion.

Near singular configurations, joint loss can dramatically reduce manipulability. Even if the remaining degrees of freedom appear sufficient numerically, the reduced Jacobian may become poorly conditioned and require very large joint velocities to produce small Cartesian motions. Singularity measures, condition numbers, and manipulability indices should therefore be reevaluated after failure and incorporated into trajectory modification and motion constraints.

Null-space control changes significantly when redundancy is reduced. During nominal operation, redundant joint motion can be used for obstacle avoidance, joint-limit avoidance, posture optimization, or energy reduction without disturbing the primary end-effector task. After a joint failure, the available null space may shrink or disappear. Secondary objectives must therefore be reprioritized so that they do not consume motion authority needed for essential tasks.

Dynamic reconfiguration must consider more than kinematics. Removing or locking a joint changes the manipulator's effective inertia, Coriolis and centrifugal terms, gravity compensation, friction behavior, and actuator loading. A controller that continues using the nominal dynamic model may generate inaccurate torque commands. The post-fault model should therefore incorporate the failed joint constraint and updated dynamics whenever model-based torque control is used.

A locked joint can transmit substantial reaction torque through neighboring links. Healthy actuators may consequently experience higher loads than during nominal operation, particularly when carrying a payload. Reconfiguration must verify that redistributed joint torques remain within continuous and peak limits. Thermal constraints, gearbox ratings, brake capacity, structural loads, and power availability should be considered before allowing continued operation.

Partial actuator degradation requires a different strategy from complete joint removal. If a joint retains limited torque, it may remain useful within a constrained operating region. The controller can represent its available torque as \|τ_j\| ≤ τ_j,max,fault and distribute motion or force demands accordingly. This preserves more capability than complete exclusion while preventing the degraded actuator from being commanded beyond its verified residual capacity.

Control allocation and optimization provide a systematic way to redistribute demands among healthy joints. A constrained optimizer can minimize tracking error, joint effort, or energy while respecting torque limits, velocity limits, joint ranges, collision constraints, and the failed joint condition. Priority-weighted objectives allow essential end-effector behavior to dominate while less important performance goals are progressively relaxed as available control authority decreases.

Trajectory replanning is often required because the original path may pass through configurations that are no longer feasible after failure. A fault-aware planner can search for a new path within the reduced configuration space while avoiding obstacles, singularities, joint limits, and excessive actuator loads. Replanning may target the original destination, an intermediate recovery pose, a payload release location, or a predefined minimum-risk configuration.

Collision avoidance becomes more challenging because joint failure changes both reachable motion and the geometry of possible escape paths. A locked joint may prevent the manipulator from retracting along its normal trajectory. The planner should evaluate self-collision, environmental collision, and human proximity using the post-fault kinematic model. Conservative safety margins may be appropriate because motion capability and prediction accuracy have been reduced.

Payload handling requires special attention during reconfiguration. If the manipulator is carrying an object when a joint fails, gravity and inertial loads can make some recovery motions unsafe. The system should determine whether the remaining joints can support the payload statically and dynamically. Depending on capability, the safest response may be to maintain position, lower the payload, transfer it to a support surface, or activate a mechanical holding mechanism.

Joint brakes can provide valuable fault-containment capability. When an actuator loses torque generation but the joint remains mechanically controllable, engaging a brake can transform an uncontrolled or free-moving joint into a known locked constraint. The manipulator can then reconfigure around the fixed joint. Brake engagement must be coordinated carefully to avoid impact loads or sudden configuration changes during motion.

Sensor failure at a joint should be distinguished from actuator failure because the mechanical degree of freedom may remain controllable. Redundant encoders, motor-side sensing, observers, or kinematic estimation may reconstruct joint position or velocity sufficiently for degraded operation. If trustworthy state estimation cannot be maintained, however, the joint may need to be immobilized or excluded even though its actuator hardware remains functional.

Communication failure can similarly create an apparent joint failure. A distributed servo may stop receiving commands or transmitting feedback while its mechanical hardware remains healthy. Timeout monitoring, sequence counters, network diagnostics, and local drive status help distinguish communication loss from actuator or sensor faults. Local safe behavior at the joint controller can prevent uncontrolled motion while higher-level reconfiguration is performed.

The transition from nominal control to fault-reconfigured control must be smooth. Abruptly changing the active joint set, dynamic model, or task objective can introduce discontinuities in velocity and torque commands. Bumpless transfer, command blending, state initialization, acceleration limiting, and temporary damping can reduce transients. The transition should preserve stability even when diagnosis occurs during high-speed or loaded motion.

A useful supervisory architecture can represent operation through states such as normal, fault suspected, fault isolated, reconfiguring, degraded operation, and safe stop. Transitions depend on diagnostic confidence, remaining controllability, payload state, collision risk, and actuator margins. Separating supervisory decisions from low-level servo loops allows safety policy to coordinate trajectory planning, inverse kinematics, dynamics, and joint controllers consistently.

Reconfiguration feasibility should be evaluated before continued operation is authorized. The system must determine whether the remaining joints provide sufficient rank, workspace, torque capability, sensing, and braking authority for the intended degraded task. A successful numerical inverse-kinematics solution alone is not sufficient. Dynamic loads, uncertainty, collision margins, and the ability to reach a safe state after another disturbance must also be considered.

Graceful degradation provides an appropriate strategy when full task recovery is impossible. The manipulator may reduce speed and acceleration, restrict workspace, simplify orientation requirements, lower payload limits, disable high-force operations, or execute only predefined recovery motions. These restrictions preserve useful functionality without assuming that a mechanically degraded arm retains the same operational envelope as a healthy system.

Verification requires systematic joint-failure injection across representative manipulator configurations. Tests should include locked joints, free joints, reduced torque, increased friction, encoder loss, delayed servo response, communication interruption, and brake activation. Simulation, software-in-the-loop, hardware-in-the-loop, and controlled physical experiments can evaluate detection latency, reconfiguration time, trajectory feasibility, stability, collision margins, and residual actuator loads.

Testing should also examine failures near singularities, joint limits, obstacles, and high-payload configurations because these conditions can expose limitations that are hidden during nominal poses. The ability to achieve a safe configuration is often more important than maintaining the original task. Verification should therefore demonstrate that the controller recognizes infeasible recovery attempts and transitions to an alternative minimum-risk strategy rather than persisting with unsafe commands.

Ultimately, manipulator reconfiguration on joint failure transforms a fixed nominal controller into a capability-aware fault-tolerant system. By identifying the failed joint, constructing reduced kinematic and dynamic models, evaluating remaining controllability, reprioritizing tasks, and replanning feasible motion, the manipulator can use its surviving degrees of freedom intelligently. Continued operation is permitted only within verified residual capability, while safe recovery takes priority when the original task can no longer be maintained.

관절 고장 시 매니퓰레이터 재구성(Manipulator Reconfiguration on Joint Failure)은 하나 이상의 관절이 정상적인 기능을 상실한 이후에도 로봇 팔이 안전하고 유용한 운동을 유지할 수 있도록 하는 고장 허용 제어(Fault-Tolerant Control) 전략이다. 각 관절은 매니퓰레이터의 위치, 자세, 힘 생성 능력 및 도달 가능 작업 공간(Reachable Workspace)에 기여하므로 관절 고장은 로봇의 운동학적 및 동역학적 특성을 즉시 변화시킬 수 있다. 재구성은 이러한 상황에서 남아 있는 능력에 맞추어 제어 목표를 조정한다.

관절 고장(Joint Failure)은 액추에이터, 변속기(Transmission), 브레이크, 엔코더, 모터 드라이브, 전력 전자장치(Power Electronics), 통신 인터페이스 또는 기계 구조에서 발생할 수 있다. 그 영향은 토크 감소와 마찰 증가에서부터 제어되지 않는 운동, 위치 센싱 상실 또는 완전한 잠김까지 다양하다. 따라서 효과적인 재구성은 어떤 관절에 문제가 발생했는지뿐만 아니라 해당 고장이 어떠한 물리적 거동을 발생시키는지 판단하는 결함 탐지 및 분리(Fault Detection and Isolation)에서 시작된다.

관절 고장은 일반적으로 남아 있는 제어 권한(Control Authority)의 수준에 따라 분류할 수 있다. 자유 운동 관절(Free-Swinging Joint)은 능동 토크를 상실하지만 수동적인 운동은 가능할 수 있으며, 잠긴 관절(Locked Joint)은 특정 각도에서 고정된다. 부분적으로 성능이 저하된 관절은 제한된 토크나 속도를 계속 생성할 수 있다. 이러한 상태는 서로 다른 고장 후 운동학(Post-Fault Kinematics)을 발생시키므로 제어기는 단순히 명령을 비활성화하는 것이 아니라 고장 관절의 상태를 명시적으로 표현해야 한다.

n 자유도(n-Degree-of-Freedom)를 가진 매니퓰레이터에서 공칭 관절 벡터(Nominal Joint Vector) q는 모든 능동 좌표를 포함한다. 관절 j가 q_j = q_j,f에서 잠기면 해당 좌표는 고정 파라미터가 되고 실질적으로 제어 가능한 자유도의 수가 감소한다. 이후 남아 있는 능동 관절을 이용하여 순기구학 모델(Forward Kinematic Model)을 다시 계산해야 한다. 이를 통해 축소된 구성 공간(Reduced Configuration Space)과 관절 운동 및 말단장치 운동 사이의 새로운 매핑을 얻는다.

매니퓰레이터 자코비안(Manipulator Jacobian)은 고장 후 재구성에서 핵심적인 역할을 한다. 정상 운전에서 말단장치 속도는 ẋ = J(q)q̇로 표현할 수 있다. 하나의 관절을 더 이상 사용할 수 없게 되면 해당 자코비안 열(Jacobian Column)은 명령된 운동에 자유롭게 기여할 수 없다. 정상적으로 제어 가능한 관절로 축소 자코비안(Reduced Jacobian) J_r을 구성하고, 실행 가능한 직교 좌표계 속도 명령은 이 축소된 매핑의 치역 공간(Range Space) 내에 존재해야 한다.

하나의 관절을 상실했다고 해서 항상 작업 전체를 수행할 수 없게 되는 것은 아니다. 중복 매니퓰레이터(Redundant Manipulator)는 특정 말단장치 작업에 필요한 것보다 많은 관절을 가질 수 있다. 예를 들어 6차원 자세 작업을 수행하는 7자유도 로봇 팔은 구성 상태에 따라 하나의 관절이 고정된 이후에도 상당한 기능을 유지할 수 있다. 재구성은 이러한 운동학적 중복성(Kinematic Redundancy)을 활용하여 충분한 독립 운동 방향이 남아 있는 경우 가장 우선순위가 높은 작업을 유지한다.

관절 고장 이후에는 도달 가능 작업 공간(Reachable Workspace)이 변화하므로 이를 명시적으로 평가해야 한다. 일부 직교 좌표계 위치는 계속 도달할 수 있지만 특정 자세는 구현할 수 없게 될 수 있으며, 다른 영역은 실행 가능한 작업 공간에서 완전히 사라질 수 있다. 고장 후 제어기는 도달할 수 없는 목표를 명령하지 않아야 한다. 불가능한 자세를 지속적으로 달성하려 하면 포화(Saturation), 과도한 내부 힘, 불안정한 최적화 또는 정상 관절에 대한 불필요한 부하가 발생할 수 있다.

전체 운동 능력을 유지할 수 없는 경우 작업 우선순위화(Task Prioritization)가 필수적이다. 충돌 회피(Collision Avoidance), 탑재물 안정화, 지지 상태 유지 또는 사람으로부터 안전하게 이동하는 것과 같은 안전 중요 목표는 정확한 궤적 추종보다 높은 우선순위를 가져야 한다. 선호 자세, 에너지 최적화 또는 정밀한 방향 제어와 같은 낮은 우선순위 목표는 남아 있는 자유도가 모든 요구사항을 동시에 만족시키기에 부족할 경우 완화할 수 있다.

작업 공간 재구성(Task-Space Reconfiguration)은 사용 가능한 이동 능력에 따라 명령된 말단장치 목표를 변경할 수 있다. 완전한 6차원 위치 및 자세 제어가 더 이상 불가능하다면 하나 이상의 자세 축을 완화하면서 3차원 위치를 유지할 수 있다. 반대로 일부 작업에서는 정확한 위치보다 도구 자세를 유지하는 것이 더 중요할 수 있다. 선택되는 축소 작업(Reduced Task)은 임무 요구사항과 안전상의 결과를 반영해야 한다.

역기구학(Inverse Kinematics)은 관절 고장 이후 다시 구성되어야 한다. 모든 관절이 제어 가능하다고 가정하는 기존 역해법(Inverse Solution)은 고장 난 좌표에 반복적으로 명령을 전달하거나 실행 불가능한 관절 속도를 생성할 수 있다. 결함 인지 역기구학(Fault-Aware Inverse Kinematics)은 영향을 받은 관절을 제거하거나 제한하고 나머지 변수를 이용하여 해를 계산한다. 의사역행렬(Pseudoinverse), 감쇠 최소제곱법(Damped Least-Squares), 제약 최적화(Constrained Optimization), 이차계획법(Quadratic Programming)을 이용하여 실행 가능한 고장 후 운동을 계산할 수 있다.

특이점(Singular Configuration) 부근에서는 관절 상실이 조작성(Manipulability)을 급격하게 감소시킬 수 있다. 남아 있는 자유도가 수치적으로 충분해 보이더라도 축소 자코비안의 조건 상태가 나빠지면 작은 직교 좌표계 운동을 생성하기 위해 매우 큰 관절 속도가 필요할 수 있다. 따라서 고장 이후 특이점 지표(Singularity Measure), 조건수(Condition Number), 조작성 지수(Manipulability Index)를 다시 평가하고 궤적 수정 및 운동 제약조건에 반영해야 한다.

널 공간 제어(Null-Space Control)는 중복성이 감소하면 크게 변화한다. 정상 운전에서는 중복 관절 운동을 이용하여 주 말단장치 작업을 방해하지 않으면서 장애물 회피, 관절 한계 회피, 자세 최적화 또는 에너지 절감을 수행할 수 있다. 관절 고장 이후에는 사용 가능한 널 공간(Null Space)이 감소하거나 완전히 사라질 수 있다. 따라서 필수 작업에 필요한 운동 권한을 이차 목표가 소비하지 않도록 보조 목표의 우선순위를 다시 설정해야 한다.

동역학적 재구성(Dynamic Reconfiguration)은 운동학만을 고려해서는 안 된다. 관절이 제거되거나 잠기면 매니퓰레이터의 유효 관성(Effective Inertia), 코리올리 및 원심력 항(Coriolis and Centrifugal Terms), 중력 보상(Gravity Compensation), 마찰 특성 및 액추에이터 부하가 변화한다. 공칭 동역학 모델을 계속 사용하는 제어기는 부정확한 토크 명령을 생성할 수 있다. 따라서 모델 기반 토크 제어를 사용하는 경우 고장 관절의 제약조건과 갱신된 동역학을 고장 후 모델에 반영해야 한다.

잠긴 관절(Locked Joint)은 인접 링크를 통해 상당한 반력 토크(Reaction Torque)를 전달할 수 있다. 따라서 정상 액추에이터는 특히 탑재물을 운반하는 상황에서 정상 운전보다 높은 부하를 받을 수 있다. 재구성 과정에서는 재분배된 관절 토크가 연속 및 최대 허용 한계 내에 있는지 확인해야 한다. 운전을 계속하기 전에 열적 제약조건, 기어박스 정격(Gearbox Rating), 브레이크 용량, 구조 하중 및 가용 전력을 고려해야 한다.

부분적인 액추에이터 성능 저하(Partial Actuator Degradation)는 관절을 완전히 제거하는 경우와 다른 전략이 필요하다. 관절에 제한적인 토크 능력이 남아 있다면 제약된 운전 영역 내에서 계속 활용할 수 있다. 제어기는 사용 가능한 토크를 \|τ_j\| ≤ τ_j,max,fault와 같이 표현하고 이에 따라 운동 또는 힘 요구를 분배할 수 있다. 이를 통해 성능이 저하된 액추에이터가 검증된 잔존 능력 이상으로 명령되는 것을 방지하면서 완전히 제외하는 것보다 많은 기능을 유지할 수 있다.

제어 할당(Control Allocation)과 최적화(Optimization)는 정상 관절 사이에 요구량을 체계적으로 재분배할 수 있는 방법을 제공한다. 제약 최적화기는 고장 관절 조건과 토크 제한, 속도 제한, 관절 범위, 충돌 제약조건을 만족하면서 추종 오차, 관절 노력 또는 에너지를 최소화할 수 있다. 우선순위 가중 목표(Priority-Weighted Objective)를 사용하면 사용 가능한 제어 권한이 감소함에 따라 덜 중요한 성능 목표를 점진적으로 완화하면서 필수적인 말단장치 동작을 우선할 수 있다.

원래의 경로가 고장 이후 더 이상 실행할 수 없는 구성 상태를 통과할 수 있으므로 궤적 재계획(Trajectory Replanning)이 필요한 경우가 많다. 결함 인지 계획기(Fault-Aware Planner)는 장애물, 특이점, 관절 한계 및 과도한 액추에이터 부하를 피하면서 축소된 구성 공간에서 새로운 경로를 탐색할 수 있다. 재계획의 목표는 원래 목적지, 중간 복구 자세(Recovery Pose), 탑재물 해제 위치 또는 사전에 정의된 최소 위험 구성(Minimum-Risk Configuration)이 될 수 있다.

관절 고장은 사용 가능한 운동과 탈출 경로의 형상을 모두 변화시키므로 충돌 회피(Collision Avoidance)는 더욱 어려워진다. 잠긴 관절은 매니퓰레이터가 정상적인 궤적을 따라 후퇴하는 것을 방해할 수 있다. 계획기는 고장 후 운동학 모델을 이용하여 자기 충돌(Self-Collision), 환경 충돌 및 사람과의 근접성을 평가해야 한다. 운동 능력과 예측 정확도가 감소한 상태이므로 보다 보수적인 안전 여유(Safety Margin)를 적용하는 것이 적절할 수 있다.

재구성 과정에서 탑재물 처리(Payload Handling)는 특별히 고려해야 한다. 관절 고장 발생 시 매니퓰레이터가 물체를 들고 있다면 중력 및 관성 하중으로 인해 일부 복구 동작이 위험해질 수 있다. 시스템은 남아 있는 관절이 탑재물을 정적 및 동적으로 지지할 수 있는지 판단해야 한다. 잔존 능력에 따라 현재 위치를 유지하거나 탑재물을 낮추고, 지지 표면으로 이동하거나, 기계식 고정 장치(Mechanical Holding Mechanism)를 작동시키는 것이 가장 안전한 대응이 될 수 있다.

관절 브레이크(Joint Brake)는 유용한 결함 격리 능력(Fault-Containment Capability)을 제공할 수 있다. 액추에이터가 토크 생성 능력을 상실했지만 관절을 기계적으로 제어할 수 있다면 브레이크를 체결하여 제어되지 않거나 자유롭게 움직이는 관절을 알려진 잠금 제약조건으로 변환할 수 있다. 이후 매니퓰레이터는 고정된 관절을 기준으로 재구성할 수 있다. 운동 중 충격 하중이나 갑작스러운 구성 변화가 발생하지 않도록 브레이크 체결은 신중하게 조정되어야 한다.

관절의 센서 고장(Sensor Failure)은 기계적인 자유도 자체는 여전히 제어 가능할 수 있으므로 액추에이터 고장과 구분해야 한다. 중복 엔코더(Redundant Encoder), 모터 측 센싱(Motor-Side Sensing), 관측기(Observer) 또는 운동학적 추정(Kinematic Estimation)을 이용하면 성능 저하 운전에 충분한 수준으로 관절 위치나 속도를 재구성할 수 있다. 그러나 신뢰할 수 있는 상태 추정을 유지할 수 없다면 액추에이터 하드웨어가 정상이라도 해당 관절을 고정하거나 제어 대상에서 제외해야 할 수 있다.

통신 고장(Communication Failure) 역시 외형적으로 관절 고장처럼 나타날 수 있다. 분산형 서보(Distributed Servo)가 명령 수신 또는 피드백 전송을 중단하더라도 기계적 하드웨어 자체는 정상일 수 있다. 타임아웃 모니터링, 시퀀스 카운터(Sequence Counter), 네트워크 진단 및 로컬 드라이브 상태를 통해 통신 손실과 액추에이터 또는 센서 결함을 구분할 수 있다. 상위 수준의 재구성이 수행되는 동안 관절 제어기의 로컬 안전 동작(Local Safe Behavior)을 통해 제어되지 않는 운동을 방지할 수 있다.

정상 제어에서 결함 재구성 제어(Fault-Reconfigured Control)로의 전환은 부드럽게 이루어져야 한다. 활성 관절 집합, 동역학 모델 또는 작업 목표를 갑작스럽게 변경하면 속도 및 토크 명령에 불연속이 발생할 수 있다. 무충격 전환(Bumpless Transfer), 명령 블렌딩(Command Blending), 상태 초기화(State Initialization), 가속도 제한 및 일시적인 감쇠(Damping)를 통해 과도응답을 줄일 수 있다. 고속 또는 고하중 운동 중에 진단이 이루어지는 경우에도 전환 과정에서 안정성이 유지되어야 한다.

유용한 감독 아키텍처(Supervisory Architecture)는 운전 상태를 정상(Normal), 결함 의심(Fault Suspected), 결함 분리(Fault Isolated), 재구성 중(Reconfiguring), 성능 저하 운전(Degraded Operation), 안전 정지(Safe Stop) 등으로 표현할 수 있다. 상태 전이는 진단 신뢰도, 잔존 제어 가능성, 탑재물 상태, 충돌 위험 및 액추에이터 여유를 기반으로 결정된다. 감독 의사결정을 저수준 서보 루프와 분리하면 안전 정책이 궤적 계획, 역기구학, 동역학 및 관절 제어기를 일관되게 조정할 수 있다.

운전 지속을 허용하기 전에 재구성 실행 가능성(Reconfiguration Feasibility)을 평가해야 한다. 시스템은 남아 있는 관절이 의도된 성능 저하 작업에 충분한 랭크(Rank), 작업 공간, 토크 능력, 센싱 및 제동 권한을 제공하는지 판단해야 한다. 수치적으로 유효한 역기구학 해를 얻었다는 사실만으로는 충분하지 않다. 동적 하중, 불확실성, 충돌 여유 및 추가적인 외란 이후에도 안전 상태에 도달할 수 있는 능력을 함께 고려해야 한다.

전체 작업 복구가 불가능한 경우 점진적 성능 저하(Graceful Degradation)가 적절한 전략을 제공한다. 매니퓰레이터는 속도와 가속도를 낮추고, 작업 공간을 제한하며, 자세 요구조건을 단순화하고, 탑재 하중 한계를 낮추거나, 고출력 작업을 비활성화하고, 사전에 정의된 복구 운동만 수행할 수 있다. 이러한 제한은 기계적으로 성능이 저하된 로봇 팔이 정상 시스템과 동일한 운용 영역을 유지한다고 가정하지 않으면서 유용한 기능을 보존한다.

검증(Verification)은 대표적인 매니퓰레이터 구성에서 체계적인 관절 고장 주입(Joint-Failure Injection)을 포함해야 한다. 시험에는 관절 잠김, 자유 운동 관절, 토크 감소, 마찰 증가, 엔코더 손실, 서보 응답 지연, 통신 중단 및 브레이크 작동 등이 포함될 수 있다. 시뮬레이션(Simulation), 소프트웨어 인 더 루프(Software-in-the-Loop, SIL), 하드웨어 인 더 루프(Hardware-in-the-Loop, HIL), 통제된 실물 실험을 통해 탐지 지연시간, 재구성 시간, 궤적 실행 가능성, 안정성, 충돌 여유 및 잔존 액추에이터 부하를 평가할 수 있다.

시험에서는 특이점, 관절 한계, 장애물 및 높은 탑재 하중 조건 근처에서 발생하는 고장도 평가해야 한다. 이러한 조건에서는 정상적인 자세에서 드러나지 않았던 재구성의 한계가 나타날 수 있다. 원래 작업을 계속 유지하는 것보다 안전한 구성 상태를 확보하는 능력이 더 중요할 수 있다. 따라서 검증 과정에서는 제어기가 실행 불가능한 복구 시도를 인식하고 위험한 명령을 지속하는 대신 대안적인 최소 위험 전략(Minimum-Risk Strategy)으로 전환할 수 있음을 입증해야 한다.

궁극적으로 관절 고장 시 매니퓰레이터 재구성(Manipulator Reconfiguration on Joint Failure)은 고정된 공칭 제어기를 시스템 능력을 인식하는 고장 허용 시스템(Capability-Aware Fault-Tolerant System)으로 전환한다. 고장 난 관절을 식별하고, 축소된 운동학 및 동역학 모델을 구성하며, 남아 있는 제어 가능성을 평가하고, 작업 우선순위를 재조정하며, 실행 가능한 운동을 재계획함으로써 매니퓰레이터는 잔존 자유도를 지능적으로 활용할 수 있다. 지속적인 운전은 검증된 잔존 능력(Verified Residual Capability) 범위 내에서만 허용되며, 기존 작업을 더 이상 유지할 수 없는 경우에는 안전한 복구가 최우선 목표가 된다.

##  

## 08.06 Degraded Mode Operation on Partial Drive Failure [w/Code]

![](images/image6.png){width="7.268055555555556in" height="7.268055555555556in"}

Degraded mode operation on partial drive failure allows a robot to continue operating safely when part of its propulsion or drive system loses performance but sufficient control authority remains. Instead of treating every drive anomaly as a complete system failure, fault-tolerant control evaluates residual capability and establishes a reduced operating envelope. The objective is controlled continuation or safe recovery rather than preservation of nominal performance.

Partial drive failure can include reduced motor torque, inverter derating, loss of one wheel drive, increased drivetrain friction, limited steering authority, brake drag, thermal restriction, voltage limitation, or intermittent command execution. These failures differ from complete propulsion loss because useful actuation remains available. The control system must identify both the affected drive channel and the magnitude of remaining force, torque, speed, and directional capability.

A convenient representation describes the effective actuator command as u_f = Λu, where u is the requested drive input and Λ contains actuator effectiveness factors. A healthy channel has an effectiveness factor near one, while a degraded channel lies between zero and one. Complete loss approaches zero. Estimating Λ allows the controller to reason quantitatively about remaining propulsion capability instead of using only binary healthy-or-failed classifications.

Detection typically begins by comparing commanded drive behavior with measured response. Motor current, wheel speed, torque estimate, vehicle acceleration, yaw rate, steering angle, temperature, and bus voltage provide complementary evidence. If commanded torque increases while wheel acceleration remains unexpectedly low, the system may infer reduced effectiveness. Persistent residuals help distinguish genuine degradation from short disturbances such as terrain variation or wheel slip.

Fault isolation is important because the appropriate degraded strategy depends on the location of the failure. Reduced torque on one side of a differential-drive robot creates asymmetric propulsion, while equal derating across all motors mainly reduces acceleration and climbing capability. A steering actuator fault affects curvature authority differently from a propulsion fault. The controller therefore requires a fault map that connects diagnosed components to resulting motion constraints.

Residual capability estimation converts diagnosis into usable control limits. For each drive channel, the system can estimate maximum positive and negative torque, achievable wheel speed, acceleration capability, thermal margin, and response bandwidth. These values form a post-fault actuator envelope. Control commands should remain inside this verified envelope because repeatedly requesting unavailable torque can cause integrator windup, overheating, instability, or further component damage.

The degraded operating envelope should also be expressed at vehicle level. Reduced wheel torque may limit longitudinal acceleration, maximum grade, payload capacity, and stopping performance. Asymmetric drive capability may restrict yaw rate or turning curvature. Steering degradation may increase minimum turning radius. Translating component-level faults into vehicle-level constraints allows motion planners and supervisory controllers to make decisions using physically meaningful limits.

Speed reduction is one of the most effective degraded-mode measures. Lower velocity reduces required acceleration, kinetic energy, stopping distance, lateral force, and disturbance sensitivity. A robot with reduced propulsion or steering authority may therefore remain controllable at a substantially lower speed even when nominal-speed operation is unsafe. Speed limits should be derived from remaining actuation and braking capability rather than selected as arbitrary fixed percentages.

Acceleration and jerk limits should normally be reduced together with speed. Rapid command changes can demand peak torque that a degraded drive can no longer produce, causing tracking errors and further saturation. Smooth reference generation limits transient load and provides additional margin for control correction. This is especially important for robots carrying payloads because aggressive acceleration can shift load distribution and increase demand on already weakened drive channels.

Control allocation redistributes propulsion demands among available actuators. In a multi-wheel platform, healthy motors can provide additional torque while a degraded motor contributes only within its verified capability. The allocation problem can minimize tracking error or energy while respecting individual torque, current, thermal, and speed limits. Redistribution should avoid simply overloading healthy actuators, because secondary failures can convert a manageable degraded condition into total loss of mobility.

For differential-drive and skid-steer robots, asymmetric drive degradation directly affects yaw control. The commanded linear velocity v and angular velocity ω must be mapped to wheel commands that remain feasible under unequal left and right drive limits. If one side cannot generate nominal torque, the feasible combinations of v and ω shrink. The motion controller should therefore constrain curvature and acceleration before issuing wheel commands rather than relying on saturation afterward.

Four-wheel-drive and six-wheel-drive robots provide additional redundancy but also introduce load-distribution complexity. Loss of one motor may be compensated by other driven wheels, depending on traction, terrain, suspension geometry, and drivetrain configuration. Torque redistribution should account for normal force and available tire-ground friction. Increasing torque on a lightly loaded wheel may produce slip instead of useful propulsion and can degrade localization based on wheel odometry.

Traction estimation becomes increasingly important during degraded operation. A weak drive channel and a slipping wheel can produce similar relationships between motor speed and vehicle acceleration. IMU measurements, wheel-speed comparisons, motor current, visual or LiDAR odometry, and estimated ground velocity can help distinguish these conditions. The controller should not compensate for apparent torque loss by increasing command if the underlying problem is insufficient traction.

Braking capability must be evaluated independently from propulsion capability. A motor may lose positive drive torque while regenerative or mechanical braking remains available, or a brake fault may reduce stopping authority even when propulsion is normal. Degraded-mode decisions should therefore use a verified deceleration envelope. Maximum permitted speed may need to be determined primarily by the distance and reliability with which the robot can stop under the current fault condition.

Steering degradation requires its own operational restrictions. If steering rate or maximum steering angle is reduced, the robot may still travel safely along paths with gentle curvature but cannot execute tight turns. The planner should enlarge turning radii, avoid narrow passages, and eliminate maneuvers requiring rapid steering reversal. For independently steered wheels, remaining steering actuators may support alternative steering geometries if the mechanical configuration permits.

Thermal derating is a common form of partial drive degradation. Motor windings, inverters, gearboxes, and batteries may remain functional but require reduced continuous output as temperature approaches allowable limits. Unlike abrupt hardware failure, thermal capability changes gradually. The controller can use temperature models and measured thermal margins to reduce torque proactively, preventing protective shutdown while preserving enough propulsion for mission completion or return to a safe location.

Battery and power-system limitations can produce system-wide drive degradation. Low state of charge, voltage sag, current limiting, or a weakened power module may reduce the torque simultaneously available from several actuators. Power-aware control allocation should therefore consider a shared electrical power constraint in addition to individual motor limits. Independent motor commands that are feasible separately may become infeasible when their combined electrical demand exceeds available system power.

Mission planning should respond to the degraded envelope rather than merely modifying low-level control gains. Routes containing steep slopes, loose terrain, narrow turns, high-speed segments, or long distances may no longer be appropriate. A fault-aware planner can select flatter terrain, wider corridors, lower-speed paths, shorter recovery routes, or designated service locations. This prevents the planner from repeatedly requesting motions that the degraded controller cannot reliably execute.

Payload constraints may also need to change after partial drive failure. A loaded robot requires greater propulsion and braking force, particularly on slopes or during acceleration. The system should determine whether the current payload remains compatible with the residual drive envelope. Depending on the application, the robot may continue at reduced speed, deliver the payload through a safer route, transfer it at an intermediate location, or stop until assistance is available.

State estimation must remain reliable during degraded motion. Wheel-speed measurements from a damaged drivetrain may no longer represent vehicle velocity accurately because of slip, drag, or mechanical decoupling. The estimator can reduce confidence in affected wheel odometry and rely more strongly on IMU, vision, LiDAR, GNSS, or other independent sources. Diagnostic information should therefore modify both control allocation and sensor-fusion weighting.

Integral action requires careful management when actuators lose capability. A controller that continues integrating tracking error while the degraded drive is saturated may accumulate a large internal command and generate severe transients when conditions change. Anti-windup mechanisms, command projection, and explicit actuator constraints should be activated during degraded operation. Controller internal states may also require bumpless initialization when switching between nominal and degraded modes.

Transition into degraded mode should be smooth and deterministic. Abrupt changes in torque distribution, speed limits, steering constraints, or controller gains can themselves destabilize the vehicle. A supervisory controller can ramp commands toward the reduced envelope, blend control parameters, and coordinate braking or steering adjustments. Transition timing should be fast enough to contain the fault while avoiding unnecessary discontinuities in vehicle motion.

A degraded-mode state machine can distinguish normal operation, fault suspected, degraded mode requested, degraded mode active, recovery, and safe stop. Entry conditions should include diagnostic confidence and verified residual controllability. The system should not continue simply because some actuators still respond. It must establish that the remaining drive, steering, braking, sensing, communication, and power capabilities are sufficient for the intended reduced operation.

Operational restrictions should be communicated beyond the low-level controller. The motion planner, mission manager, remote operator, fleet-management system, and safety supervisor may need information about maximum speed, allowable curvature, prohibited terrain, payload restrictions, remaining range, and fault status. A common capability interface prevents higher-level software from assuming nominal vehicle performance after the drive system has entered a degraded state.

Recovery from degraded mode should require evidence that the original capability has been restored. Intermittent thermal, communication, or electrical faults can disappear temporarily and then recur. Hysteresis, persistence logic, self-tests, and health-history information can prevent repeated switching between nominal and degraded states. In some cases, a detected hardware degradation should remain latched until inspection even if the immediate residual returns to normal.

A safe stop remains necessary when residual capability becomes insufficient. Triggers may include loss of minimum braking authority, excessive steering asymmetry, unstable yaw response, thermal limits, insufficient power, inability to follow the constrained path, or a second drive failure. The transition should seek a minimum-risk condition by reducing speed, selecting a stable stopping location when possible, applying available brakes, and preventing unintended restart.

Verification should inject representative partial drive failures across speed, payload, terrain, temperature, and battery conditions. Tests can emulate reduced motor effectiveness, torque saturation, inverter derating, brake drag, steering limitation, wheel slip, intermittent drive loss, and power restrictions. Simulation, SIL, HIL, dynamometer testing, and physical robot trials can evaluate fault detection, residual capability estimation, reconfiguration latency, tracking stability, and stopping performance.

Testing should also examine combinations of degradation and environmental disturbances. A robot that remains controllable with one weakened motor on level pavement may fail to maintain trajectory on a slope or low-friction surface. Validation should therefore establish a multidimensional degraded operating envelope rather than a single reduced-speed value. Safety margins should account for model uncertainty, payload variation, tire condition, and diagnostic estimation error.

Ultimately, degraded mode operation transforms partial drive failure from an uncontrolled performance loss into a managed change of system capability. Diagnosis identifies the affected drive element, residual capability estimation determines what remains physically achievable, and fault-aware control restricts commands to that envelope. By coordinating control allocation, planning, state estimation, power management, and safety supervision, the robot can continue useful operation when justified and transition to a safe state when it is not.

부분 구동 고장 시 성능 저하 모드 운전(Degraded Mode Operation on Partial Drive Failure)은 추진 또는 구동 시스템의 일부가 성능을 상실하더라도 충분한 제어 권한(Control Authority)이 남아 있는 경우 로봇이 안전하게 운전을 지속할 수 있도록 한다. 모든 구동 이상을 완전한 시스템 고장으로 처리하는 대신, 고장 허용 제어(Fault-Tolerant Control)는 잔존 능력(Residual Capability)을 평가하고 축소된 운용 영역(Reduced Operating Envelope)을 설정한다. 목표는 정상 성능의 유지가 아니라 제어 가능한 운전 지속 또는 안전한 복구이다.

부분 구동 고장(Partial Drive Failure)에는 모터 토크 감소, 인버터 출력 저감(Inverter Derating), 단일 휠 구동 상실, 구동계 마찰 증가, 제한된 조향 권한, 브레이크 끌림(Brake Drag), 열적 제한, 전압 제한 또는 간헐적인 명령 실행 등이 포함될 수 있다. 이러한 고장은 유용한 구동력이 여전히 남아 있다는 점에서 완전한 추진력 상실과 다르다. 제어 시스템은 영향을 받은 구동 채널뿐만 아니라 남아 있는 힘, 토크, 속도 및 방향 제어 능력의 크기도 식별해야 한다.

효과적인 액추에이터 명령은 u_f = Λu와 같이 표현할 수 있으며, 여기서 u는 요구된 구동 입력이고 Λ는 액추에이터 유효성 계수(Actuator Effectiveness Factor)를 포함한다. 정상 채널의 유효성 계수는 1에 가깝고 성능이 저하된 채널은 0과 1 사이의 값을 가진다. 완전한 기능 상실은 0에 가까워진다. Λ를 추정하면 제어기는 단순한 정상 또는 고장 분류 대신 남아 있는 추진 능력을 정량적으로 판단할 수 있다.

고장 탐지(Detection)는 일반적으로 명령된 구동 동작과 실제 측정 응답을 비교하는 것에서 시작한다. 모터 전류, 휠 속도, 토크 추정값, 차량 가속도, 요율(Yaw Rate), 조향각, 온도 및 버스 전압은 상호 보완적인 정보를 제공한다. 요구 토크가 증가하는데 휠 가속도가 예상보다 낮다면 시스템은 구동 유효성이 감소했다고 판단할 수 있다. 지속적인 잔차(Residual)는 실제 성능 저하와 지형 변화 또는 휠 슬립(Wheel Slip)과 같은 일시적인 외란을 구분하는 데 도움이 된다.

적절한 성능 저하 전략은 고장 위치에 따라 달라지므로 결함 분리(Fault Isolation)가 중요하다. 차동 구동 로봇(Differential-Drive Robot)의 한쪽에서 토크가 감소하면 비대칭 추진력이 발생하는 반면, 모든 모터가 동일하게 출력 저감되면 주로 가속 및 등판 능력이 감소한다. 조향 액추에이터 고장은 추진 고장과 다른 방식으로 곡률 제어 권한에 영향을 준다. 따라서 제어기는 진단된 구성요소와 그 결과로 발생하는 운동 제약조건을 연결하는 결함 맵(Fault Map)을 필요로 한다.

잔존 능력 추정(Residual Capability Estimation)은 진단 결과를 실제 제어에 사용할 수 있는 제한값으로 변환한다. 각 구동 채널에 대해 최대 정·역방향 토크, 달성 가능한 휠 속도, 가속 능력, 열적 여유(Thermal Margin), 응답 대역폭을 추정할 수 있다. 이러한 값은 고장 후 액추에이터 영역(Post-Fault Actuator Envelope)을 구성한다. 사용할 수 없는 토크를 반복적으로 요구하면 적분기 와인드업(Integrator Windup), 과열, 불안정 또는 추가적인 부품 손상이 발생할 수 있으므로 제어 명령은 검증된 영역 안에서 유지되어야 한다.

성능 저하 운용 영역(Degraded Operating Envelope)은 차량 수준에서도 표현되어야 한다. 휠 토크 감소는 종방향 가속도, 최대 등판각, 탑재 능력 및 정지 성능을 제한할 수 있다. 비대칭 구동 능력은 요율이나 회전 곡률을 제한할 수 있으며 조향 성능 저하는 최소 회전 반경을 증가시킬 수 있다. 구성요소 수준의 고장을 차량 수준의 제약조건으로 변환하면 모션 플래너(Motion Planner)와 감독 제어기(Supervisory Controller)가 물리적으로 의미 있는 제한값을 이용하여 판단할 수 있다.

속도 감소(Speed Reduction)는 가장 효과적인 성능 저하 모드 대응 방법 중 하나이다. 낮은 속도는 필요한 가속도, 운동에너지, 정지거리, 횡력 및 외란 민감도를 감소시킨다. 따라서 추진 또는 조향 권한이 감소한 로봇도 정상 속도에서는 안전하지 않더라도 충분히 낮은 속도에서는 제어 가능성을 유지할 수 있다. 속도 제한은 임의의 고정 비율로 설정하기보다 남아 있는 구동 및 제동 능력을 기반으로 결정해야 한다.

가속도 및 저크 제한(Acceleration and Jerk Limits)도 일반적으로 속도와 함께 감소시켜야 한다. 급격한 명령 변화는 성능이 저하된 구동계가 더 이상 생성할 수 없는 최대 토크를 요구하여 추종 오차와 추가적인 포화를 발생시킬 수 있다. 부드러운 기준 명령 생성(Smooth Reference Generation)은 과도 부하를 제한하고 제어 보정을 위한 추가 여유를 제공한다. 이는 급격한 가속이 하중 분포를 변화시키고 이미 약화된 구동 채널의 요구량을 증가시킬 수 있는 탑재 로봇에서 특히 중요하다.

제어 할당(Control Allocation)은 사용 가능한 액추에이터 사이에서 추진 요구량을 재분배한다. 다중 휠 플랫폼에서는 정상 모터가 추가적인 토크를 제공하는 동안 성능이 저하된 모터는 검증된 능력 범위 내에서만 기여할 수 있다. 할당 문제는 개별 토크, 전류, 열적 한계 및 속도 제한을 만족하면서 추종 오차 또는 에너지를 최소화할 수 있다. 정상 액추에이터에 단순히 과부하를 집중해서는 안 되며, 이차 고장(Secondary Failure)은 관리 가능한 성능 저하 상태를 완전한 이동성 상실로 전환시킬 수 있다.

차동 구동 및 스키드 스티어 로봇(Skid-Steer Robot)에서는 비대칭 구동 성능 저하가 요 제어(Yaw Control)에 직접적인 영향을 준다. 명령된 선속도 v와 각속도 ω는 좌우 구동 제한이 서로 다른 상태에서도 실행 가능한 휠 명령으로 변환되어야 한다. 한쪽에서 정상 토크를 생성할 수 없다면 실행 가능한 v와 ω의 조합 범위가 감소한다. 따라서 모션 제어기는 포화가 발생한 이후에 대응하기보다 휠 명령을 생성하기 전에 곡률과 가속도를 제한해야 한다.

4륜 구동(Four-Wheel Drive) 및 6륜 구동(Six-Wheel Drive) 로봇은 추가적인 중복성을 제공하지만 하중 분배의 복잡성도 증가시킨다. 하나의 모터가 고장 나면 접지력, 지형, 서스펜션 형상 및 구동계 구성에 따라 다른 구동 휠이 이를 보상할 수 있다. 토크 재분배는 수직 하중(Normal Force)과 사용 가능한 타이어-지면 마찰을 고려해야 한다. 하중이 작은 휠에 토크를 증가시키면 유효 추진력 대신 슬립이 발생할 수 있으며 휠 오도메트리(Wheel Odometry)에 기반한 위치 추정 성능도 저하될 수 있다.

성능 저하 운전에서는 접지력 추정(Traction Estimation)이 더욱 중요해진다. 약화된 구동 채널과 슬립이 발생하는 휠은 모터 속도와 차량 가속도 사이에서 유사한 관계를 나타낼 수 있다. 관성 측정 장치(IMU), 휠 속도 비교, 모터 전류, 비전 또는 라이다 오도메트리(Vision or LiDAR Odometry), 추정 지면 속도를 이용하면 이러한 상태를 구분할 수 있다. 실제 문제가 접지력 부족이라면 제어기는 단순히 토크 손실로 판단하여 명령을 증가시켜서는 안 된다.

제동 능력(Braking Capability)은 추진 능력과 독립적으로 평가해야 한다. 모터가 양의 구동 토크를 상실하더라도 회생 제동(Regenerative Braking) 또는 기계식 제동은 계속 사용할 수 있으며, 반대로 추진 능력은 정상이지만 브레이크 고장으로 정지 능력이 감소할 수도 있다. 따라서 성능 저하 모드 판단에는 검증된 감속 영역(Deceleration Envelope)을 사용해야 한다. 최대 허용 속도는 현재 고장 상태에서 로봇이 얼마나 안정적이고 신뢰성 있게 정지할 수 있는지에 따라 결정될 수 있다.

조향 성능 저하(Steering Degradation)에는 별도의 운용 제한이 필요하다. 조향 속도 또는 최대 조향각이 감소하면 로봇은 완만한 곡률을 가진 경로를 따라 안전하게 이동할 수 있지만 급격한 회전은 수행할 수 없게 된다. 플래너는 회전 반경을 확대하고 좁은 통로를 피하며 빠른 조향 반전이 필요한 기동을 제거해야 한다. 독립 조향 휠(Independently Steered Wheel)의 경우 기계적 구성이 허용한다면 남아 있는 조향 액추에이터를 이용하여 대체 조향 형상(Alternative Steering Geometry)을 구성할 수 있다.

열적 출력 저감(Thermal Derating)은 일반적인 부분 구동 성능 저하 형태이다. 모터 권선, 인버터, 기어박스 및 배터리는 계속 작동할 수 있지만 온도가 허용 한계에 접근하면 연속 출력의 감소가 필요할 수 있다. 갑작스러운 하드웨어 고장과 달리 열적 능력은 점진적으로 변화한다. 제어기는 온도 모델과 측정된 열적 여유를 이용하여 선제적으로 토크를 감소시킴으로써 보호 정지를 방지하면서 임무 완료 또는 안전한 위치로 복귀하는 데 필요한 추진력을 유지할 수 있다.

배터리 및 전력 시스템 제한(Battery and Power-System Limitation)은 시스템 전체의 구동 성능 저하를 발생시킬 수 있다. 낮은 충전 상태(State of Charge), 전압 강하, 전류 제한 또는 약화된 전력 모듈은 여러 액추에이터에서 동시에 사용할 수 있는 토크를 감소시킬 수 있다. 따라서 전력 인지 제어 할당(Power-Aware Control Allocation)은 개별 모터 제한뿐만 아니라 공유되는 전기적 전력 제약조건도 고려해야 한다. 각각 독립적으로 실행 가능한 모터 명령도 결합된 전력 요구량이 시스템의 가용 전력을 초과하면 실행 불가능할 수 있다.

임무 계획(Mission Planning)은 단순히 저수준 제어 이득을 수정하는 것이 아니라 성능 저하 영역 자체에 대응해야 한다. 급경사, 느슨한 지면, 좁은 회전 구간, 고속 구간 또는 장거리 이동이 포함된 경로는 더 이상 적합하지 않을 수 있다. 결함 인지 플래너(Fault-Aware Planner)는 평탄한 지형, 넓은 통로, 저속 경로, 짧은 복귀 경로 또는 지정된 정비 위치를 선택할 수 있다. 이를 통해 플래너가 성능 저하 제어기로 안정적으로 수행할 수 없는 운동을 반복적으로 요구하는 것을 방지한다.

부분 구동 고장 이후에는 탑재물 제약조건(Payload Constraint)도 변경해야 할 수 있다. 하중을 운반하는 로봇은 특히 경사로나 가속 상황에서 더 큰 추진력과 제동력을 필요로 한다. 시스템은 현재 탑재물이 잔존 구동 영역과 호환되는지 판단해야 한다. 응용 환경에 따라 로봇은 감소된 속도로 계속 운전하거나 더 안전한 경로로 탑재물을 운반하고, 중간 위치에서 탑재물을 인계하거나, 지원이 제공될 때까지 정지할 수 있다.

성능 저하 운동 중에도 상태 추정(State Estimation)은 신뢰성을 유지해야 한다. 손상된 구동계의 휠 속도 측정값은 슬립, 끌림 또는 기계적 분리로 인해 더 이상 차량 속도를 정확하게 나타내지 못할 수 있다. 추정기는 영향을 받은 휠 오도메트리의 신뢰도를 낮추고 관성 측정 장치(IMU), 비전, 라이다(LiDAR), 위성항법시스템(GNSS) 또는 기타 독립적인 센싱 정보를 더 강하게 활용할 수 있다. 따라서 진단 정보는 제어 할당뿐만 아니라 센서 융합 가중치에도 반영되어야 한다.

액추에이터가 성능을 상실하면 적분 동작(Integral Action)을 신중하게 관리해야 한다. 성능이 저하된 구동계가 포화된 상태에서도 제어기가 추종 오차를 계속 적분하면 내부 명령이 크게 누적되어 조건이 변화할 때 심각한 과도응답을 발생시킬 수 있다. 성능 저하 운전에서는 안티 와인드업(Anti-Windup), 명령 투영(Command Projection), 명시적 액추에이터 제약조건을 활성화해야 한다. 정상 모드와 성능 저하 모드 사이를 전환할 때 제어기 내부 상태에 대한 무충격 초기화(Bumpless Initialization)가 필요할 수도 있다.

성능 저하 모드로의 전환은 부드럽고 결정론적이어야 한다. 토크 분배, 속도 제한, 조향 제약조건 또는 제어기 이득이 갑자기 변화하면 이러한 전환 자체가 차량을 불안정하게 만들 수 있다. 감독 제어기(Supervisory Controller)는 명령을 축소된 운용 영역으로 점진적으로 이동시키고 제어 파라미터를 블렌딩하며 제동 또는 조향 조정을 통합할 수 있다. 전환 시간은 고장을 충분히 빠르게 억제하면서 차량 운동에 불필요한 불연속을 발생시키지 않아야 한다.

성능 저하 모드 상태 기계(Degraded-Mode State Machine)는 정상 운전(Normal Operation), 결함 의심(Fault Suspected), 성능 저하 모드 요청(Degraded Mode Requested), 성능 저하 모드 활성(Degraded Mode Active), 복구(Recovery), 안전 정지(Safe Stop)를 구분할 수 있다. 진입 조건에는 진단 신뢰도와 검증된 잔존 제어 가능성(Residual Controllability)이 포함되어야 한다. 일부 액추에이터가 여전히 응답한다는 이유만으로 운전을 지속해서는 안 되며, 남아 있는 구동, 조향, 제동, 센싱, 통신 및 전력 능력이 의도된 축소 운전에 충분한지 확인해야 한다.

운용 제한(Operational Restriction)은 저수준 제어기 이외의 시스템에도 전달되어야 한다. 모션 플래너, 임무 관리자(Mission Manager), 원격 운용자(Remote Operator), 플릿 관리 시스템(Fleet-Management System), 안전 감독기(Safety Supervisor)는 최대 속도, 허용 곡률, 금지 지형, 탑재 제한, 잔여 주행 거리 및 결함 상태 정보를 필요로 할 수 있다. 공통 능력 인터페이스(Common Capability Interface)는 구동 시스템이 성능 저하 상태에 진입한 이후에도 상위 수준 소프트웨어가 정상 차량 성능을 가정하는 것을 방지한다.

성능 저하 모드에서 복구하려면 원래의 기능이 회복되었다는 근거가 필요하다. 간헐적인 열적, 통신 또는 전기적 결함은 일시적으로 사라졌다가 다시 발생할 수 있다. 히스테리시스(Hysteresis), 지속성 논리(Persistence Logic), 자체 진단(Self-Test), 건전성 이력(Health History)을 이용하면 정상 모드와 성능 저하 모드 사이에서 반복적으로 전환되는 것을 방지할 수 있다. 일부 하드웨어 성능 저하는 즉각적인 잔차가 정상으로 돌아오더라도 점검이 이루어질 때까지 고장 상태를 래치(Latch)하여 유지해야 할 수 있다.

잔존 능력이 충분하지 않으면 안전 정지(Safe Stop)가 필요하다. 최소 제동 권한 상실, 과도한 조향 비대칭, 불안정한 요 응답, 열적 한계 도달, 전력 부족, 제한된 경로 추종 실패 또는 두 번째 구동 고장 등이 안전 정지의 트리거가 될 수 있다. 전환 과정에서는 속도를 감소시키고 가능한 경우 안정적인 정지 위치를 선택하며 사용 가능한 브레이크를 작동하고 의도하지 않은 재시작을 방지하여 최소 위험 상태(Minimum-Risk Condition)를 확보해야 한다.

검증(Verification)은 다양한 속도, 탑재물, 지형, 온도 및 배터리 조건에서 대표적인 부분 구동 고장을 주입해야 한다. 시험에서는 모터 유효성 감소, 토크 포화, 인버터 출력 저감, 브레이크 끌림, 조향 제한, 휠 슬립, 간헐적 구동 손실 및 전력 제한을 모사할 수 있다. 시뮬레이션(Simulation), 소프트웨어 인 더 루프(Software-in-the-Loop, SIL), 하드웨어 인 더 루프(Hardware-in-the-Loop, HIL), 동력계 시험(Dynamometer Testing), 실물 로봇 시험을 통해 고장 탐지, 잔존 능력 추정, 재구성 지연시간, 추종 안정성 및 정지 성능을 평가할 수 있다.

시험에서는 성능 저하와 환경 외란(Environmental Disturbance)이 결합된 상황도 평가해야 한다. 하나의 모터가 약화된 상태에서도 평탄한 포장면에서는 제어 가능한 로봇이 경사로나 저마찰 노면에서는 궤적을 유지하지 못할 수 있다. 따라서 검증에서는 하나의 감소된 속도 값이 아니라 다차원 성능 저하 운용 영역(Multidimensional Degraded Operating Envelope)을 확립해야 한다. 안전 여유는 모델 불확실성, 탑재물 변화, 타이어 상태 및 진단 추정 오차를 고려해야 한다.

궁극적으로 성능 저하 모드 운전(Degraded Mode Operation)은 부분 구동 고장을 제어되지 않는 성능 손실에서 관리 가능한 시스템 능력 변화로 전환한다. 진단을 통해 영향을 받은 구동 요소를 식별하고, 잔존 능력 추정을 통해 물리적으로 달성 가능한 범위를 결정하며, 결함 인지 제어(Fault-Aware Control)를 통해 명령을 해당 영역 안으로 제한한다. 제어 할당, 경로 계획, 상태 추정, 전력 관리 및 안전 감독을 통합함으로써 로봇은 정당한 조건에서는 유용한 운전을 지속하고, 그렇지 않은 경우에는 안전 상태로 전환할 수 있다.

##  

## 08.07 Predictive Health Monitoring (PHM) and Control Integration

![](images/image7.png){width="7.268055555555556in" height="7.268055555555556in"}

Predictive Health Monitoring (PHM) extends fault-tolerant control from reacting to detected failures toward anticipating degradation before functional loss occurs. Instead of representing components only as healthy or failed, PHM estimates evolving condition, degradation rate, and future capability. Integrating these estimates with robot control enables motion, workload, and mission decisions to consider both immediate performance and expected component health.

A PHM architecture begins with continuous acquisition of health-relevant signals from actuators, sensors, power electronics, batteries, transmissions, brakes, bearings, and computing hardware. Motor current, voltage, temperature, vibration, torque, speed, position error, communication statistics, and energy consumption can reveal gradual degradation. Operational context such as payload, terrain, velocity, ambient temperature, and duty cycle is also required to interpret these signals correctly.

Raw measurements usually require preprocessing before they can support reliable health assessment. Filtering removes irrelevant noise, synchronization aligns signals from distributed devices, and normalization accounts for operating conditions. Features such as RMS vibration, spectral energy, temperature rise rate, current imbalance, torque residual, friction estimate, and tracking error can then provide compact indicators of component condition while preserving information related to degradation.

Condition monitoring determines whether current behavior differs meaningfully from an expected healthy baseline. The baseline may be obtained from physical models, commissioning data, fleet statistics, or learned representations. Residuals between measured and expected behavior can reveal abnormal trends even when the robot still satisfies its control objectives. This distinction is important because early degradation often appears in internal health indicators before visible task performance deteriorates.

Health indicators should reflect degradation continuously rather than only triggering binary alarms. A normalized health index may range from healthy operation to a critical condition and can combine multiple features using statistical models, state observers, machine learning, or physics-informed estimation. Confidence should accompany the health estimate because sensor noise, changing environments, limited training data, and uncertain degradation mechanisms can make an apparently precise health value misleading.

Diagnostics identifies the component and probable fault mechanism associated with abnormal health indicators. For a drive system, increased current and temperature together with reduced acceleration may indicate mechanical resistance, while elevated vibration at characteristic frequencies may suggest bearing or gearbox deterioration. Combining multiple signals improves isolation because individual symptoms can otherwise be explained by payload variation, terrain resistance, thermal conditions, or temporary disturbances.

Prognostics estimates how component health is expected to evolve in the future. A central quantity is Remaining Useful Life (RUL), which represents the predicted time, cycles, distance, or accumulated workload before a defined performance or safety threshold is reached. RUL is not an absolute failure time; it is an estimate conditioned on current measurements, assumed future usage, degradation models, and uncertainty about operating conditions.

Physics-based prognostics uses knowledge of wear, thermal stress, fatigue, battery aging, lubrication, or mechanical loading to predict degradation. Data-driven methods instead learn relationships between historical sensor patterns and future health outcomes. Hybrid approaches combine physical constraints with learned models, often providing better interpretability than purely data-driven methods while adapting more effectively than fixed analytical models to complex real-world behavior.

Uncertainty management is essential in prognostics. An RUL estimate should ideally be represented with confidence bounds or a probability distribution rather than as a single deterministic number. Uncertainty can arise from sensor errors, incomplete fault knowledge, variation between components, future mission conditions, and model approximation. Control decisions can then become more conservative as uncertainty increases, especially when consequences of unexpected failure are severe.

PHM becomes operationally powerful when its outputs are integrated directly with control. A conventional controller attempts to minimize tracking error using available actuator capability, whereas a health-aware controller can also consider how control effort affects degradation. High torque, rapid acceleration, repeated thermal cycling, excessive vibration, or sustained operation near actuator limits may achieve immediate performance while accelerating wear and reducing future capability.

Health-aware control can therefore introduce degradation cost into an optimization objective. A model predictive controller, for example, may minimize tracking error and energy consumption while penalizing predicted thermal stress or component damage. The resulting command may be slightly slower than the nominal optimum but can reduce cumulative degradation. This creates a systematic tradeoff between mission performance and preservation of component life.

Control allocation can use health information to distribute workload across redundant actuators. If several motors can produce the required force, a component showing early degradation can receive less torque while healthier actuators assume additional load within their limits. The objective is not necessarily to eliminate use of the degraded component, because excessive redistribution may accelerate wear elsewhere. Instead, allocation should balance performance, thermal margins, efficiency, and fleet-level maintenance objectives.

Health-aware trajectory planning extends this principle beyond individual actuator commands. A robot with declining drivetrain health may avoid steep slopes, rough terrain, aggressive turns, or long high-speed segments. A manipulator with a degrading joint may choose configurations that reduce torque on that joint. By selecting paths and postures with lower predicted damage accumulation, the planner can preserve useful life while still satisfying essential mission requirements.

Mission management can use predicted health to determine whether a task should begin, continue, be modified, or terminate. A robot may have enough capability to complete its current action but insufficient predicted margin for the entire planned mission and safe return. PHM allows the mission manager to evaluate expected workload against remaining health, enabling earlier decisions such as shortened routes, reduced payload, intermediate charging, maintenance return, or controlled task handover.

The integration should distinguish health state from instantaneous fault state. A component can be functional but degrading, degraded but stable, or apparently healthy with high prognostic uncertainty. Suitable states may include healthy, aging, warning, degraded, critical, and failed. Transitions should consider trend persistence and confidence so that temporary operating disturbances do not cause unnecessary mission changes or repeated switching between health modes.

Thresholds for health intervention should depend on operational risk. A low-risk indoor transport robot may tolerate greater degradation before restricting operation, while a heavy manipulator carrying a suspended payload may require larger safety margins. Intervention thresholds can incorporate component criticality, redundancy availability, stopping capability, environmental exposure, human proximity, and the time required to reach a safe maintenance location.

PHM and fault-tolerant control operate on different but complementary time scales. Fault detection may respond within milliseconds to an abrupt actuator failure, while prognostics can evaluate degradation over hours, days, months, or accumulated cycles. A unified architecture should allow fast protection functions to override long-term optimization whenever immediate safety is threatened, while slower health-aware planning shapes operation when sufficient time remains for preventive action.

Digital twins can support PHM by maintaining a continuously updated representation of component condition and expected behavior. Measured loads, temperatures, operating cycles, and environmental exposure can update model parameters over time. The twin can simulate future mission scenarios to estimate health consumption under alternative routes or control policies, allowing the system to compare not only whether a mission is feasible but also how strongly it may consume remaining life.

Fleet-level PHM provides additional information unavailable to a single robot. Similar robots operating under different workloads can contribute degradation histories, failure signatures, and maintenance outcomes. Population statistics can improve anomaly thresholds and RUL models, while individual adaptation accounts for unit-specific behavior. Fleet learning should preserve traceability so that changes in health models can be validated before influencing safety-related control decisions.

Maintenance planning is a natural extension of PHM-control integration. Instead of servicing components only at fixed intervals or after failure, maintenance can be scheduled according to predicted condition and mission demand. A robot approaching a health threshold may complete a low-risk task and then return for inspection. Maintenance records can subsequently update prognostic models, closing the loop between field operation, service actions, and future health estimation.

Communication of health information should use standardized and interpretable interfaces. Controllers and planners need quantities such as available torque, thermal margin, health index, uncertainty, predicted RUL, and recommended operating limits rather than unstructured diagnostic messages. Each estimate should include timestamps, validity, confidence, and source information so that stale or low-quality health predictions do not silently influence safety-critical decisions.

Cybersecurity and data integrity are also relevant because PHM increasingly depends on networked sensors, logs, cloud analytics, and fleet databases. Corrupted health data could cause unnecessary derating or conceal actual degradation. Authentication, integrity checks, secure updates, diagnostic plausibility tests, and local fallback models help ensure that control decisions remain safe when external health services or communication links are unavailable.

Fail-safe behavior remains necessary when prognostic confidence becomes insufficient. If health estimates disagree, critical sensors are unavailable, degradation accelerates unexpectedly, or remaining capability falls below a verified threshold, the controller should transition from life-extending optimization to immediate risk reduction. Depending on the robot, this may involve reduced speed, payload stabilization, movement to a safe area, controlled shutdown, or activation of redundant hardware.

Verification of PHM-control integration requires more than demonstrating accurate fault classification. Tests should evaluate health-index tracking, degradation detection latency, RUL accuracy, uncertainty calibration, control adaptation, and resulting safety margins. Accelerated aging experiments, fault injection, simulation, SIL, HIL, and long-duration field data can expose interactions between diagnostic errors and control actions that short nominal tests may not reveal.

Validation should also examine false prognostic decisions. Overly pessimistic health estimates can reduce productivity and cause unnecessary maintenance, while optimistic estimates can allow operation beyond safe capability. Experiments should therefore measure how prediction errors propagate into speed limits, workload allocation, route selection, mission completion, and safe-stop decisions. Robust integration requires acceptable behavior even when the prognostic model is imperfect.

A key design principle is that PHM should provide decision support rather than unrestricted authority over low-level safety functions. Prognostic information can optimize how remaining capability is used, but independently verified limits for torque, temperature, speed, braking, and collision avoidance should remain enforceable. This separation prevents uncertain long-term predictions from overriding deterministic protections required for immediate robot safety.

Ultimately, PHM and control integration creates a feedback loop between component condition and robot behavior. Sensors and operational data estimate current health, prognostic models predict future capability, and controllers adapt workload and motion to reduce risk and manage degradation. The resulting operating history then becomes new evidence for subsequent health estimation. This closed-loop relationship enables robots to move from reactive fault handling toward condition-aware, predictive, and progressively self-managing operation.

예측 건전성 모니터링(Predictive Health Monitoring, PHM)은 고장 허용 제어(Fault-Tolerant Control)를 탐지된 고장에 대응하는 수준에서 기능 상실이 발생하기 전에 성능 저하를 예측하는 수준으로 확장한다. 구성요소를 단순히 정상 또는 고장 상태로 표현하는 대신, PHM은 변화하는 상태, 성능 저하 속도 및 미래의 가용 능력을 추정한다. 이러한 추정값을 로봇 제어와 통합하면 운동, 작업 부하 및 임무에 대한 의사결정에서 현재의 성능뿐만 아니라 예상되는 구성요소의 건전성까지 고려할 수 있다.

PHM 아키텍처는 액추에이터, 센서, 전력 전자장치(Power Electronics), 배터리, 변속기(Transmission), 브레이크, 베어링 및 컴퓨팅 하드웨어로부터 건전성과 관련된 신호를 지속적으로 획득하는 것에서 시작한다. 모터 전류, 전압, 온도, 진동, 토크, 속도, 위치 오차, 통신 통계 및 에너지 소비량은 점진적인 성능 저하를 나타낼 수 있다. 이러한 신호를 올바르게 해석하려면 탑재물, 지형, 속도, 주변 온도 및 듀티 사이클(Duty Cycle)과 같은 운용 상황 정보도 필요하다.

원시 측정값(Raw Measurement)은 일반적으로 신뢰할 수 있는 건전성 평가에 사용되기 전에 전처리(Preprocessing)가 필요하다. 필터링은 불필요한 노이즈를 제거하고, 동기화는 분산 장치에서 획득한 신호의 시간을 정렬하며, 정규화(Normalization)는 운용 조건의 차이를 보상한다. 이후 RMS 진동, 스펙트럼 에너지, 온도 상승률, 전류 불균형, 토크 잔차, 마찰 추정값 및 추종 오차와 같은 특징값(Feature)은 성능 저하와 관련된 정보를 유지하면서 구성요소 상태를 간결하게 나타낼 수 있다.

상태 모니터링(Condition Monitoring)은 현재의 동작이 예상되는 정상 기준선(Healthy Baseline)과 의미 있게 다른지를 판단한다. 기준선은 물리 모델, 초기 시운전 데이터(Commissioning Data), 플릿 통계(Fleet Statistics) 또는 학습된 표현으로부터 얻을 수 있다. 측정된 동작과 예상 동작 사이의 잔차(Residual)는 로봇이 아직 제어 목표를 만족하는 상황에서도 비정상적인 추세를 나타낼 수 있다. 초기 성능 저하는 외부에서 관찰되는 작업 성능이 악화되기 전에 내부 건전성 지표에서 먼저 나타나는 경우가 많기 때문에 이러한 구분은 중요하다.

건전성 지표(Health Indicator)는 단순한 이진 경보만 발생시키는 것이 아니라 성능 저하를 연속적으로 나타내야 한다. 정규화된 건전성 지수(Health Index)는 정상 운전에서 임계 상태까지의 범위를 나타낼 수 있으며 통계 모델, 상태 관측기(State Observer), 기계학습(Machine Learning) 또는 물리 정보 기반 추정(Physics-Informed Estimation)을 이용하여 여러 특징값을 결합할 수 있다. 센서 노이즈, 변화하는 환경, 제한된 학습 데이터 및 불확실한 열화 메커니즘으로 인해 겉보기에 정밀한 건전성 값도 오해를 일으킬 수 있으므로 건전성 추정값에는 신뢰도(Confidence)가 함께 제공되어야 한다.

진단(Diagnostics)은 비정상적인 건전성 지표와 관련된 구성요소 및 가능한 고장 메커니즘을 식별한다. 구동 시스템에서 전류와 온도가 증가하면서 가속도가 감소한다면 기계적 저항 증가를 의미할 수 있으며, 특정 주파수에서 진동이 증가하면 베어링 또는 기어박스 성능 저하를 나타낼 수 있다. 개별 증상은 탑재물 변화, 지형 저항, 열적 조건 또는 일시적인 외란으로도 설명될 수 있으므로 여러 신호를 결합하면 결함 분리(Fault Isolation) 성능을 향상시킬 수 있다.

예지(Prognostics)는 구성요소의 건전성이 미래에 어떻게 변화할 것인지를 추정한다. 핵심적인 값은 잔여 유효 수명(Remaining Useful Life, RUL)으로, 정의된 성능 또는 안전 임계값에 도달하기까지 예상되는 시간, 사이클, 거리 또는 누적 작업량을 나타낸다. RUL은 절대적인 고장 시점을 의미하지 않으며 현재 측정값, 가정된 미래 사용 조건, 성능 저하 모델 및 운용 조건의 불확실성에 기반한 추정값이다.

물리 기반 예지(Physics-Based Prognostics)는 마모, 열적 스트레스, 피로, 배터리 노화, 윤활 또는 기계적 하중에 대한 지식을 이용하여 성능 저하를 예측한다. 반면 데이터 기반 방법(Data-Driven Method)은 과거 센서 패턴과 미래 건전성 결과 사이의 관계를 학습한다. 하이브리드 접근법(Hybrid Approach)은 물리적 제약조건과 학습 모델을 결합하며, 순수한 데이터 기반 방법보다 높은 해석 가능성을 제공하면서 복잡한 실제 환경의 동작에는 고정된 해석 모델보다 효과적으로 적응할 수 있다.

예지에서는 불확실성 관리(Uncertainty Management)가 필수적이다. RUL 추정값은 하나의 결정론적 숫자가 아니라 신뢰 구간(Confidence Bounds) 또는 확률 분포(Probability Distribution)로 표현하는 것이 바람직하다. 불확실성은 센서 오차, 불완전한 고장 지식, 구성요소 간 편차, 미래 임무 조건 및 모델 근사에서 발생할 수 있다. 예상하지 못한 고장의 결과가 심각한 경우에는 불확실성이 증가함에 따라 제어 의사결정을 더욱 보수적으로 수행할 수 있다.

PHM의 출력이 제어와 직접 통합되면 운용 측면에서 더욱 강력한 기능을 제공한다. 기존 제어기는 사용 가능한 액추에이터 능력을 이용하여 추종 오차를 최소화하려 하지만, 건전성 인지 제어기(Health-Aware Controller)는 제어 노력이 성능 저하에 어떠한 영향을 미치는지도 고려할 수 있다. 높은 토크, 급격한 가속, 반복적인 열 사이클, 과도한 진동 또는 액추에이터 한계 근처에서의 지속 운전은 즉각적인 성능을 달성할 수 있지만 마모를 가속하고 미래의 가용 능력을 감소시킬 수 있다.

따라서 건전성 인지 제어(Health-Aware Control)는 최적화 목적함수에 성능 저하 비용(Degradation Cost)을 포함할 수 있다. 예를 들어 모델 예측 제어(Model Predictive Control, MPC)는 추종 오차와 에너지 소비를 최소화하는 동시에 예측된 열적 스트레스 또는 구성요소 손상을 페널티로 적용할 수 있다. 그 결과 생성되는 명령은 공칭 최적값보다 약간 느릴 수 있지만 누적 성능 저하를 줄일 수 있다. 이를 통해 임무 성능과 구성요소 수명 보존 사이의 절충 관계를 체계적으로 관리할 수 있다.

제어 할당(Control Allocation)은 건전성 정보를 이용하여 중복 액추에이터 사이에서 작업 부하를 분배할 수 있다. 여러 모터가 필요한 힘을 생성할 수 있는 경우 초기 성능 저하가 나타나는 구성요소에는 더 적은 토크를 할당하고 정상적인 액추에이터에는 허용 범위 내에서 추가적인 부하를 분배할 수 있다. 성능이 저하된 구성요소를 완전히 사용하지 않는 것이 항상 최적은 아니다. 과도한 재분배가 다른 구성요소의 마모를 가속할 수 있으므로 성능, 열적 여유, 효율 및 플릿 수준 유지보수 목표를 균형 있게 고려해야 한다.

건전성 인지 궤적 계획(Health-Aware Trajectory Planning)은 이러한 원리를 개별 액추에이터 명령보다 높은 수준으로 확장한다. 구동계의 건전성이 감소하는 로봇은 급경사, 거친 지형, 급격한 회전 또는 장시간의 고속 구간을 피할 수 있다. 특정 관절의 성능이 저하되는 매니퓰레이터는 해당 관절에 가해지는 토크를 감소시키는 자세를 선택할 수 있다. 예측되는 손상 누적량이 낮은 경로와 자세를 선택함으로써 플래너는 필수적인 임무 요구사항을 만족하면서 유효 수명을 보존할 수 있다.

임무 관리(Mission Management)는 예측된 건전성을 이용하여 작업을 시작하거나 지속하거나 수정하거나 종료할지를 결정할 수 있다. 로봇이 현재 작업을 완료할 능력은 충분하지만 전체 계획된 임무와 안전한 복귀까지 수행할 수 있는 예측 여유가 부족할 수 있다. PHM을 이용하면 임무 관리자가 예상 작업량과 잔여 건전성을 비교하여 경로 단축, 탑재량 감소, 중간 충전, 유지보수 위치로의 복귀 또는 제어된 작업 인계와 같은 조기 의사결정을 수행할 수 있다.

통합 시스템에서는 건전성 상태(Health State)와 순간적인 결함 상태(Instantaneous Fault State)를 구분해야 한다. 구성요소는 기능적으로 정상 동작하지만 성능이 저하되는 중일 수 있고, 성능이 저하되었지만 안정적인 상태일 수도 있으며, 외관상 정상이어도 예지 불확실성이 높을 수 있다. 적절한 상태에는 정상(Healthy), 노화(Aging), 경고(Warning), 성능 저하(Degraded), 임계(Critical), 고장(Failed)이 포함될 수 있다. 일시적인 운용 외란이 불필요한 임무 변경이나 건전성 모드의 반복적인 전환을 유발하지 않도록 상태 전이에는 추세 지속성과 신뢰도를 고려해야 한다.

건전성 개입 임계값(Health Intervention Threshold)은 운용 위험에 따라 달라져야 한다. 위험도가 낮은 실내 운송 로봇은 운전을 제한하기 전에 더 큰 성능 저하를 허용할 수 있지만, 매달린 탑재물을 운반하는 대형 매니퓰레이터는 더 큰 안전 여유를 필요로 할 수 있다. 개입 임계값에는 구성요소 중요도, 중복성 가용성, 정지 능력, 환경 노출, 사람과의 근접성 및 안전한 유지보수 위치까지 이동하는 데 필요한 시간을 반영할 수 있다.

PHM과 고장 허용 제어는 서로 다르지만 상호 보완적인 시간 척도(Time Scale)에서 동작한다. 고장 탐지는 갑작스러운 액추에이터 고장에 수 밀리초 이내로 대응할 수 있는 반면, 예지는 수 시간, 수일, 수개월 또는 누적 사이클에 걸친 성능 저하를 평가할 수 있다. 통합 아키텍처에서는 즉각적인 안전이 위협받는 경우 빠른 보호 기능이 장기 최적화를 우선적으로 무시할 수 있어야 하며, 예방 조치를 수행할 시간이 충분한 경우에는 느린 건전성 인지 계획이 운용 방식을 조정해야 한다.

디지털 트윈(Digital Twin)은 구성요소 상태와 예상 동작에 대한 지속적으로 갱신되는 표현을 유지함으로써 PHM을 지원할 수 있다. 측정된 하중, 온도, 운전 사이클 및 환경 노출 정보를 이용하여 시간에 따라 모델 파라미터를 갱신할 수 있다. 디지털 트윈은 대체 경로나 제어 정책에 따른 미래 임무 시나리오를 시뮬레이션하여 건전성 소비량(Health Consumption)을 추정할 수 있으며, 이를 통해 시스템은 임무 수행 가능성뿐만 아니라 해당 임무가 잔여 수명을 얼마나 소비할지도 비교할 수 있다.

플릿 수준 PHM(Fleet-Level PHM)은 단일 로봇에서는 얻을 수 없는 추가적인 정보를 제공한다. 서로 다른 작업 부하에서 운용되는 유사한 로봇들은 성능 저하 이력, 고장 특징 및 유지보수 결과를 제공할 수 있다. 모집단 통계(Population Statistics)를 이용하여 이상 탐지 임계값과 RUL 모델을 개선할 수 있으며, 개별 적응(Individual Adaptation)을 통해 각 장비의 고유한 동작 특성을 반영할 수 있다. 안전 관련 제어 의사결정에 영향을 미치기 전에 건전성 모델의 변경 사항을 검증할 수 있도록 플릿 학습(Fleet Learning)은 추적 가능성(Traceability)을 유지해야 한다.

유지보수 계획(Maintenance Planning)은 PHM과 제어 통합의 자연스러운 확장이다. 구성요소를 고정된 주기에만 정비하거나 고장 발생 이후에 정비하는 대신, 예측된 상태와 임무 요구에 따라 유지보수를 계획할 수 있다. 건전성 임계값에 접근하는 로봇은 저위험 작업을 완료한 후 점검을 위해 복귀할 수 있다. 이후 유지보수 기록을 예지 모델에 반영함으로써 현장 운용, 정비 작업 및 미래 건전성 추정 사이에 폐루프(Closed Loop)를 구성할 수 있다.

건전성 정보의 전달에는 표준화되고 해석 가능한 인터페이스(Standardized and Interpretable Interface)를 사용해야 한다. 제어기와 플래너에는 비구조화된 진단 메시지보다 가용 토크, 열적 여유, 건전성 지수, 불확실성, 예측 RUL 및 권장 운용 제한과 같은 정량적인 정보가 필요하다. 각 추정값에는 타임스탬프, 유효성, 신뢰도 및 정보 출처를 포함하여 오래되었거나 품질이 낮은 건전성 예측값이 안전 중요 의사결정에 알지 못하는 상태로 영향을 미치지 않도록 해야 한다.

PHM이 네트워크 센서, 로그, 클라우드 분석 및 플릿 데이터베이스에 점점 더 의존하기 때문에 사이버보안(Cybersecurity)과 데이터 무결성(Data Integrity)도 중요하다. 손상된 건전성 데이터는 불필요한 출력 제한을 발생시키거나 실제 성능 저하를 숨길 수 있다. 인증(Authentication), 무결성 검사, 보안 업데이트, 진단 타당성 검사 및 로컬 대체 모델(Local Fallback Model)을 이용하면 외부 건전성 서비스나 통신 링크를 사용할 수 없는 경우에도 제어 의사결정을 안전하게 유지할 수 있다.

예지 신뢰도가 충분하지 않은 경우에는 페일세이프 동작(Fail-Safe Behavior)이 필요하다. 건전성 추정값이 서로 불일치하거나 핵심 센서를 사용할 수 없거나 성능 저하가 예상보다 빠르게 진행되거나 잔존 능력이 검증된 임계값 아래로 감소하면 제어기는 수명 연장 최적화에서 즉각적인 위험 감소로 전환해야 한다. 로봇의 종류에 따라 속도 감소, 탑재물 안정화, 안전 영역으로의 이동, 제어된 종료(Controlled Shutdown) 또는 중복 하드웨어 활성화가 수행될 수 있다.

PHM-제어 통합(PHM-Control Integration)의 검증은 정확한 고장 분류를 입증하는 것만으로는 충분하지 않다. 시험에서는 건전성 지수 추종, 성능 저하 탐지 지연시간, RUL 정확도, 불확실성 보정(Uncertainty Calibration), 제어 적응 및 결과적인 안전 여유를 평가해야 한다. 가속 노화 시험(Accelerated Aging Test), 결함 주입(Fault Injection), 시뮬레이션, 소프트웨어 인 더 루프(Software-in-the-Loop, SIL), 하드웨어 인 더 루프(Hardware-in-the-Loop, HIL), 장기간 현장 데이터를 이용하면 짧은 정상 시험에서는 발견하기 어려운 진단 오류와 제어 동작 사이의 상호작용을 확인할 수 있다.

검증에서는 잘못된 예지 판단(False Prognostic Decision)의 영향도 평가해야 한다. 지나치게 비관적인 건전성 추정은 생산성을 감소시키고 불필요한 유지보수를 발생시킬 수 있으며, 지나치게 낙관적인 추정은 안전한 능력을 넘어서는 운전을 허용할 수 있다. 따라서 시험에서는 예측 오차가 속도 제한, 작업 부하 할당, 경로 선택, 임무 완료 및 안전 정지 결정에 어떻게 전파되는지를 측정해야 한다. 강인한 통합(Robust Integration)은 예지 모델이 완벽하지 않은 경우에도 허용 가능한 동작을 보장해야 한다.

핵심 설계 원칙은 PHM이 저수준 안전 기능에 제한 없는 제어 권한을 갖는 것이 아니라 의사결정 지원(Decision Support)을 제공해야 한다는 것이다. 예지 정보는 남아 있는 능력을 어떻게 활용할지 최적화할 수 있지만 토크, 온도, 속도, 제동 및 충돌 회피에 대한 독립적으로 검증된 제한은 항상 강제될 수 있어야 한다. 이러한 분리는 불확실한 장기 예측이 즉각적인 로봇 안전에 필요한 결정론적 보호 기능(Deterministic Protection)을 무시하는 것을 방지한다.

궁극적으로 PHM과 제어 통합은 구성요소 상태와 로봇 동작 사이에 피드백 루프(Feedback Loop)를 형성한다. 센서 및 운용 데이터는 현재 건전성을 추정하고, 예지 모델은 미래의 가용 능력을 예측하며, 제어기는 위험을 감소시키고 성능 저하를 관리하도록 작업 부하와 운동을 조정한다. 그 결과로 생성되는 운용 이력은 다시 이후의 건전성 추정을 위한 새로운 근거가 된다. 이러한 폐루프 관계(Closed-Loop Relationship)를 통해 로봇은 반응형 고장 대응에서 상태 인지형(Condition-Aware), 예측형(Predictive), 그리고 점진적으로 자율 관리되는(Self-Managing) 운용 체계로 발전할 수 있다.

##  

## 08.08 Fault Tolerant Control Verification: Fault Injection Test

![](images/image8.png){width="7.268055555555556in" height="7.268055555555556in"}

Fault-tolerant control verification establishes whether a robotic system can detect, isolate, and safely respond to failures while maintaining defined levels of control performance. Verification must demonstrate more than nominal operation because fault-tolerant behavior is specifically intended for abnormal conditions. The test process therefore evaluates the complete chain from fault occurrence and detection through diagnosis, control reconfiguration, degraded operation, and safe recovery.

Fault injection testing intentionally introduces controlled failures into the robot system to reproduce realistic abnormal conditions. Faults may be injected at the sensor, actuator, communication, power, computation, or mechanical levels. The objective is not simply to make the robot fail, but to verify that the diagnostic and control architecture recognizes the injected condition, limits its consequences, and transitions to an appropriate operating state within the required response time.

Sensor fault injection can reproduce bias, drift, noise increase, frozen measurements, intermittent data, incorrect scaling, out-of-range values, timestamp errors, packet loss, and complete sensor loss. The injected fault should be applied under representative operating conditions because detection performance can depend strongly on vehicle speed, acceleration, payload, vibration, temperature, and environmental complexity. Verification should measure detection latency, false alarms, isolation accuracy, and the resulting effect on state estimation.

Actuator fault injection evaluates the ability to maintain control when commanded and realized motion become inconsistent. Reduced torque effectiveness, saturation, delayed response, increased friction, actuator locking, intermittent output, communication loss, and complete drive failure can be reproduced. For redundant systems, the test should verify that control allocation redistributes commands within the remaining actuator limits and that healthy actuators are not driven beyond their verified thermal, electrical, mechanical, or dynamic capabilities.

Communication fault injection is important for distributed robotic architectures in which sensors, actuators, controllers, and supervisory computers exchange time-sensitive information. Packet loss, excessive latency, jitter, corrupted messages, sequence errors, connection interruption, and stale data can be introduced deliberately. The verification process should confirm that timeout handling, data validity checks, local fallback behavior, and safe-state transitions prevent communication abnormalities from becoming uncontrolled robot motion.

Power-system fault injection evaluates behavior under reduced or unstable electrical capability. Voltage sag, current limitation, battery derating, power-module failure, thermal protection, and controlled loss of an electrical branch can be reproduced. The controller should recognize reduced available power and adapt actuator commands accordingly. Tests should verify that power limitations do not cause unexpected actuator saturation, excessive battery stress, loss of braking authority, or unstable transitions between operating modes.

Fault injection should cover multiple operating points rather than a single laboratory condition. Representative tests should include low and high speed, different payloads, slopes, rough surfaces, low-friction conditions, temperature extremes, and varying battery states. For manipulators, representative joint configurations, payload positions, singularity proximity, and workspace boundaries should also be considered. This ensures that fault-tolerant behavior is evaluated across the actual operating envelope.

The verification process should evaluate both detection performance and post-fault control performance. Important measures include fault detection time, isolation time, false detection rate, missed detection rate, residual tracking error, settling time, stability margin, actuator utilization, thermal response, stopping distance, and recovery time. A system should not be considered successful merely because it identifies the correct fault if the subsequent control response produces unsafe motion or violates remaining actuator limits.

Fault injection should also test fault combinations when system safety analysis identifies them as relevant. A sensor fault combined with an actuator fault, or a communication failure occurring during degraded operation, can produce diagnostic ambiguity that is not visible during single-fault testing. Multiple-fault scenarios should be selected according to system architecture and safety requirements rather than being generated without a defined purpose. The objective is to identify credible combinations that could invalidate assumed redundancy.

Hardware-in-the-loop and software-in-the-loop testing provide important intermediate verification stages. Software-in-the-loop allows large numbers of fault scenarios to be executed rapidly using simulated plant dynamics, while hardware-in-the-loop introduces real controllers, interfaces, timing behavior, and communication hardware. Physical robot testing is then used to validate the most important scenarios under real mechanical, electrical, sensing, and environmental conditions.

Fault injection must include timing verification because a correct response that occurs too late may still be unsafe. The complete latency from fault occurrence to detection, isolation, reconfiguration, and stabilization should be measured. High-criticality faults may require deterministic responses within a small number of control cycles, whereas slowly developing thermal or health-monitoring faults can operate on longer time scales. Timing requirements should therefore be linked to the physical consequences of each failure mode.

Verification should examine recovery and return-to-normal behavior as carefully as fault entry. Intermittent faults may temporarily disappear, and thermal or communication conditions may recover without repairing the underlying problem. The controller should use persistence, hysteresis, self-test results, and health history before restoring full control authority. Tests should confirm that repeated switching between nominal and degraded modes does not create instability, excessive actuator stress, or unnecessary mission interruptions.

A complete fault-tolerant verification process should maintain traceability between requirements, fault models, injection mechanisms, expected responses, measured results, and acceptance criteria. Each safety-relevant fault should have a defined expected system response and measurable pass condition. Test results should be reproducible and linked to software versions, hardware configurations, parameter sets, environmental conditions, and injected fault characteristics so that unexpected behavior can be investigated systematically.

Ultimately, fault injection verification demonstrates whether fault-tolerant control functions as an integrated safety mechanism rather than as a collection of independent diagnostic features. A robust system should detect credible failures, isolate them with sufficient confidence, estimate remaining capability, reconfigure control within verified limits, and transition to degraded or safe operation when necessary. Verification therefore provides evidence that the robot can maintain controlled behavior not only during normal operation but also when components, communication paths, sensors, actuators, or power systems behave abnormally.

고장 허용 제어 검증(Fault-Tolerant Control Verification)은 로봇 시스템이 고장을 탐지하고 분리하며 안전하게 대응하는 동시에 정의된 수준의 제어 성능을 유지할 수 있는지를 확인한다. 검증은 정상 운전만을 입증해서는 안 된다. 고장 허용 기능은 본질적으로 비정상 조건을 대상으로 하기 때문이다. 따라서 시험 과정에서는 고장 발생과 탐지부터 진단, 제어 재구성(Control Reconfiguration), 성능 저하 운전(Degraded Operation), 안전 복구(Safe Recovery)까지 전체 과정을 평가해야 한다.

고장 주입 시험(Fault Injection Testing)은 실제적인 비정상 조건을 재현하기 위해 로봇 시스템에 통제된 고장을 의도적으로 발생시키는 방법이다. 고장은 센서, 액추에이터, 통신, 전력, 연산 또는 기계적 계층에서 주입할 수 있다. 목적은 단순히 로봇을 고장 상태로 만드는 것이 아니라 진단 및 제어 아키텍처가 주입된 상태를 인식하고, 그 영향을 제한하며, 요구되는 응답 시간 안에 적절한 운전 상태로 전환하는지를 검증하는 것이다.

센서 고장 주입(Sensor Fault Injection)은 바이어스(Bias), 드리프트(Drift), 노이즈 증가, 측정값 고정(Frozen Measurement), 간헐적 데이터, 잘못된 스케일링(Incorrect Scaling), 범위 초과값, 타임스탬프 오류, 패킷 손실 및 센서 완전 상실을 재현할 수 있다. 주입된 고장은 대표적인 운전 조건에서 적용해야 하는데, 탐지 성능이 차량 속도, 가속도, 탑재물, 진동, 온도 및 환경 복잡도에 크게 의존할 수 있기 때문이다. 검증에서는 탐지 지연시간, 오경보(False Alarm), 결함 분리 정확도 및 상태 추정에 미치는 영향을 측정해야 한다.

액추에이터 고장 주입(Actuator Fault Injection)은 명령된 운동과 실제 발생한 운동 사이에 불일치가 발생하는 상황에서도 제어를 유지할 수 있는 능력을 평가한다. 토크 유효성 감소, 포화(Saturation), 응답 지연, 마찰 증가, 액추에이터 잠김, 간헐적 출력, 통신 손실 및 완전한 구동 고장을 재현할 수 있다. 중복 시스템에서는 제어 할당(Control Allocation)이 남아 있는 액추에이터의 제한 범위 내에서 명령을 재분배하는지 확인해야 하며, 정상 액추에이터가 검증된 열적, 전기적, 기계적 또는 동역학적 능력을 초과하여 구동되지 않는지도 확인해야 한다.

분산형 로봇 아키텍처에서는 센서, 액추에이터, 제어기 및 감독 컴퓨터가 시간에 민감한 정보를 서로 교환하므로 통신 고장 주입(Communication Fault Injection)이 중요하다. 패킷 손실, 과도한 지연시간, 지터(Jitter), 손상된 메시지, 시퀀스 오류, 연결 중단 및 오래된 데이터(Stale Data)를 의도적으로 발생시킬 수 있다. 검증 과정에서는 타임아웃 처리, 데이터 유효성 검사, 로컬 대체 동작(Local Fallback Behavior) 및 안전 상태 전환이 통신 이상으로 인해 로봇의 제어되지 않는 운동이 발생하는 것을 방지하는지 확인해야 한다.

전력 시스템 고장 주입(Power-System Fault Injection)은 전기적 능력이 감소하거나 불안정해지는 상황에서 시스템의 동작을 평가한다. 전압 강하, 전류 제한, 배터리 출력 저감(Battery Derating), 전력 모듈 고장, 열 보호(Thermal Protection) 및 전기 분기의 제어된 손실을 재현할 수 있다. 제어기는 사용 가능한 전력이 감소했음을 인식하고 이에 따라 액추에이터 명령을 조정해야 한다. 시험에서는 전력 제한으로 인해 예상하지 못한 액추에이터 포화, 과도한 배터리 스트레스, 제동 권한 상실 또는 운전 모드 사이의 불안정한 전환이 발생하지 않는지 검증해야 한다.

고장 주입은 하나의 실험실 조건에만 국한되지 않고 여러 운전점(Operating Point)을 대상으로 해야 한다. 대표적인 시험에는 저속 및 고속, 다양한 탑재물, 경사면, 거친 노면, 저마찰 조건, 극한 온도 및 다양한 배터리 상태가 포함되어야 한다. 매니퓰레이터의 경우 대표적인 관절 구성, 탑재물 위치, 특이점(Singularity) 근접 상태 및 작업 공간 경계도 고려해야 한다. 이를 통해 실제 운용 영역 전체에서 고장 허용 동작을 평가할 수 있다.

검증 과정에서는 고장 탐지 성능과 고장 발생 이후의 제어 성능을 모두 평가해야 한다. 주요 평가 항목에는 고장 탐지 시간, 결함 분리 시간, 오탐지율(False Detection Rate), 미탐지율(Missed Detection Rate), 잔류 추종 오차, 정착 시간(Settling Time), 안정성 여유(Stability Margin), 액추에이터 사용률, 열적 응답, 정지 거리 및 복구 시간이 포함된다. 올바른 고장을 식별했다는 사실만으로는 시스템이 성공했다고 판단해서는 안 되며, 이후의 제어 응답이 위험한 운동을 발생시키거나 남아 있는 액추에이터 제한을 위반해서는 안 된다.

시스템 안전 분석에서 관련성이 있다고 판단되는 경우 고장 조합(Fault Combination)도 시험해야 한다. 센서 고장과 액추에이터 고장이 동시에 발생하거나 성능 저하 운전 중 통신 고장이 발생하면 단일 고장 시험에서는 나타나지 않는 진단 모호성(Diagnostic Ambiguity)이 발생할 수 있다. 다중 고장 시나리오는 목적 없이 무작위로 생성하기보다 시스템 아키텍처와 안전 요구사항에 따라 선정해야 한다. 목적은 가정된 중복성을 무효화할 수 있는 현실적인 고장 조합을 식별하는 것이다.

하드웨어 인 더 루프(Hardware-in-the-Loop, HIL)와 소프트웨어 인 더 루프(Software-in-the-Loop, SIL) 시험은 중요한 중간 검증 단계이다. 소프트웨어 인 더 루프는 시뮬레이션된 플랜트 동역학을 이용하여 많은 고장 시나리오를 빠르게 실행할 수 있으며, 하드웨어 인 더 루프는 실제 제어기, 인터페이스, 시간 동작 및 통신 하드웨어를 포함한다. 이후 실제 로봇 시험을 통해 가장 중요한 시나리오를 실제 기계적, 전기적, 센싱 및 환경 조건에서 검증한다.

고장 주입은 정확한 응답뿐만 아니라 응답 시간도 검증해야 한다. 올바른 대응이라도 너무 늦게 발생하면 안전하지 않을 수 있기 때문이다. 고장 발생부터 탐지, 분리, 재구성 및 안정화까지의 전체 지연시간을 측정해야 한다. 높은 중요도를 가진 고장은 소수의 제어 주기 이내에 결정론적으로 대응해야 할 수 있는 반면, 서서히 진행되는 열적 고장이나 건전성 모니터링(Health Monitoring) 고장은 더 긴 시간 척도에서 처리할 수 있다. 따라서 시간 요구사항은 각 고장 모드가 발생시키는 물리적 결과와 연결되어야 한다.

검증에서는 고장 진입(Fault Entry)뿐만 아니라 복구 및 정상 상태 복귀(Return-to-Normal)도 동일하게 신중하게 평가해야 한다. 간헐적인 고장은 일시적으로 사라질 수 있으며, 열적 또는 통신 조건도 근본적인 문제를 해결하지 않은 상태에서 일시적으로 회복될 수 있다. 제어기는 정상적인 제어 권한을 복원하기 전에 지속성(Persistence), 히스테리시스(Hysteresis), 자체 진단 결과 및 건전성 이력을 이용해야 한다. 시험에서는 정상 모드와 성능 저하 모드 사이의 반복적인 전환이 불안정성, 과도한 액추에이터 스트레스 또는 불필요한 임무 중단을 발생시키지 않는지 확인해야 한다.

완전한 고장 허용 검증 프로세스는 요구사항, 고장 모델, 주입 방법, 예상 응답, 측정 결과 및 합격 기준 사이의 추적성(Traceability)을 유지해야 한다. 안전과 관련된 각각의 고장은 정의된 시스템 응답과 측정 가능한 합격 조건을 가져야 한다. 시험 결과는 재현 가능해야 하며 소프트웨어 버전, 하드웨어 구성, 파라미터 세트, 환경 조건 및 주입된 고장 특성과 연결되어야 한다. 이를 통해 예상하지 못한 동작을 체계적으로 조사할 수 있다.

궁극적으로 고장 주입 검증(Fault Injection Verification)은 고장 허용 제어가 서로 독립적인 진단 기능들의 집합이 아니라 통합된 안전 메커니즘으로 작동하는지를 입증한다. 강건한 시스템은 현실적인 고장을 탐지하고, 충분한 신뢰도로 이를 분리하며, 잔존 능력(Residual Capability)을 추정하고, 검증된 제한 범위 내에서 제어를 재구성하며, 필요한 경우 성능 저하 또는 안전 운전으로 전환해야 한다. 따라서 검증은 구성요소, 통신 경로, 센서, 액추에이터 또는 전력 시스템이 비정상적으로 동작하는 상황에서도 로봇이 정상 운전뿐만 아니라 제어된 상태를 유지할 수 있음을 입증하는 근거를 제공한다.

##  

## 08.09 UAV Motor Failure Fault Tolerant Control Case [w/Code]

![](images/image9.png){width="7.268055555555556in" height="7.268055555555556in"}

A UAV motor failure is a representative fault-tolerant control case because multirotor aircraft depend on coordinated thrust generation to maintain altitude, attitude, and trajectory. When one motor loses part or all of its thrust capability, the aircraft experiences an immediate change in total vertical force and control moment. A fault-tolerant flight-control system must detect the failure, estimate the remaining control authority, redistribute thrust among healthy motors, and modify the flight envelope to maintain controllable and safe operation.

For a multirotor UAV, each motor contributes not only vertical thrust but also a specific roll, pitch, and yaw moment according to its location and rotation direction. Under nominal conditions, the flight controller distributes the required total thrust and moments through a motor mixing or control-allocation matrix. When one motor fails, the original allocation becomes invalid because one control input can no longer generate the expected force. The controller must therefore construct a fault-aware allocation that reflects the actual remaining actuator effectiveness.

Motor failure can occur in several forms, including complete thrust loss, reduced thrust, excessive motor resistance, inverter failure, propeller damage, motor overheating, electrical disconnection, or intermittent drive behavior. These conditions have different effects on controllability. A partially degraded motor may still contribute useful thrust, whereas a completely failed motor may provide no positive thrust and may introduce aerodynamic drag. Fault classification should therefore estimate the remaining thrust capability rather than using only a binary failed or healthy state.

Failure detection can combine motor command, electrical current, estimated thrust, rotational speed, inertial measurements, and vehicle response. If a commanded motor speed increases but the expected vehicle acceleration or attitude response does not occur, a motor or propeller fault may be suspected. A residual can be calculated between predicted and measured aircraft behavior. Persistent residuals that cannot be explained by wind, payload changes, aggressive maneuvering, or sensor errors provide stronger evidence of an actual motor fault.

The flight controller should isolate the failed motor before performing aggressive reconfiguration. Sensor faults, aerodynamic disturbances, actuator saturation, and motor failures can produce similar short-term deviations in attitude or acceleration. Cross-checking motor speed, current, commanded thrust, IMU measurements, and estimated angular acceleration improves isolation confidence. Once the affected motor is identified, its available thrust can be reduced in the control-allocation model, and the controller can calculate the feasible range of remaining force and moments.

A critical consequence of single-motor failure is loss of independent control authority. In a conventional quadrotor, four motors normally provide sufficient independent actuation for three rotational axes and collective thrust. Removing one motor generally prevents the aircraft from maintaining the complete nominal four-axis control objective because the remaining actuators cannot independently reproduce every required combination of thrust and moment. The control strategy must therefore prioritize the most important states and accept controlled degradation of less critical objectives.

For example, maintaining sufficient altitude and controlling roll and pitch may receive higher priority than maintaining nominal yaw authority. Depending on motor arrangement and failure location, the aircraft may require a controlled yaw rotation caused by an unavoidable residual moment. The controller can compensate partially by redistributing thrust among the remaining motors while limiting the resulting rotational rate. The objective is not necessarily to maintain perfect attitude, but to keep the aircraft within a stable and recoverable flight condition.

Control allocation after motor failure can be formulated as a constrained optimization problem. The optimizer receives desired total thrust and moments together with the available thrust limits of the healthy motors and zero or reduced authority for the failed motor. It then determines feasible motor commands while respecting maximum thrust, minimum thrust, motor response limits, battery current, and thermal constraints. Priority weights can ensure that altitude and attitude stability receive greater importance than secondary trajectory objectives.

The reduced actuator set also changes the UAV flight envelope. Maximum climb rate, acceleration, maneuverability, yaw authority, and allowable payload may decrease after failure. Wind tolerance can also be reduced because the aircraft has less control margin for rejecting external disturbances. The flight-control system should therefore establish degraded limits for velocity, acceleration, angular rate, and commanded trajectory curvature instead of allowing the navigation system to continue requesting nominal performance.

Energy management becomes particularly important because the healthy motors may need to operate closer to their maximum thrust. Higher motor loading increases current consumption and thermal stress, while the battery may experience greater voltage sag. A controller that compensates for the failed motor by maximizing all remaining motors can therefore create a secondary power or thermal failure. Thrust redistribution must maintain sufficient reserve margin so that healthy motors remain capable of responding to additional disturbances.

The UAV should transition into a predefined degraded or emergency flight mode after the motor failure is confirmed. Depending on vehicle architecture and mission requirements, the response may include reducing forward velocity, limiting maneuvering, maintaining a conservative altitude, avoiding aggressive turns, and initiating a return or landing procedure. The selected response must account for the remaining controllability, available landing area, battery energy, wind conditions, payload, and proximity of people or obstacles.

Trajectory replanning is required when the original mission path cannot be safely maintained. A fault-aware planner can select a shorter route, reduce speed, avoid high-wind regions, increase clearance from obstacles, or proceed directly toward a suitable landing or recovery location. For autonomous UAVs, this decision should be coordinated between the flight controller and mission manager so that the navigation system does not continue issuing commands that exceed the degraded aircraft capability.

Motor failure during different flight phases produces different levels of risk. A failure during hover immediately changes thrust balance and may cause rapid attitude deviation, while a failure during forward flight interacts with aerodynamic forces and existing control moments. Failure during takeoff, landing, aggressive maneuvering, or high-altitude operation may require different emergency responses. Verification should therefore evaluate motor failures across representative phases rather than assuming that a single failure response is valid for every flight condition.

A robust UAV architecture should also consider the possibility of a second failure after the initial motor loss. Once one motor has failed, the remaining motors operate with reduced margin, making another actuator failure potentially unrecoverable. The controller should continuously monitor residual thrust capability and determine whether continued flight remains justified. If the remaining control authority falls below a verified threshold, the system should transition from degraded flight to an emergency landing or other minimum-risk procedure.

Verification of the motor-failure case should combine simulation, software-in-the-loop, hardware-in-the-loop, and controlled flight testing. Test scenarios should include complete thrust loss, partial thrust reduction, delayed motor response, intermittent operation, motor overheating, propeller damage, and power interruption. Evaluation should measure detection time, isolation accuracy, altitude deviation, attitude error, angular-rate response, motor utilization, battery current, recovery time, and landing performance.

Ultimately, the UAV motor-failure case demonstrates how fault-tolerant control converts actuator failure into a managed reduction of aircraft capability. The system detects abnormal motor behavior, isolates the affected actuator, estimates remaining thrust authority, reallocates available control effort, restricts the flight envelope, and coordinates degraded flight or emergency recovery. The essential principle is to avoid demanding unavailable control authority and instead use the remaining actuators within verified limits while continuously evaluating whether controlled flight remains possible.

무인항공기(UAV) 모터 고장은 고장 허용 제어(Fault-Tolerant Control)의 대표적인 사례이다. 멀티로터 항공기는 고도, 자세 및 궤적을 유지하기 위해 여러 모터의 추력을 서로 협조하여 생성하기 때문이다. 하나의 모터가 추력의 일부 또는 전부를 상실하면 전체 수직력과 제어 모멘트가 즉시 변화한다. 고장 허용 비행 제어 시스템은 고장을 탐지하고, 잔존 제어 권한(Control Authority)을 추정하며, 정상 모터 사이에서 추력을 재분배하고, 제어 가능한 안전 운전을 유지하기 위해 비행 영역을 수정해야 한다.

멀티로터 UAV에서 각 모터는 수직 추력뿐만 아니라 장착 위치와 회전 방향에 따라 특정 롤(Roll), 피치(Pitch), 요(Yaw) 모멘트에도 기여한다. 정상 조건에서는 비행 제어기가 모터 믹싱(Motor Mixing) 또는 제어 할당(Control Allocation) 행렬을 이용하여 필요한 총 추력과 모멘트를 각 모터에 분배한다. 하나의 모터가 고장 나면 하나의 제어 입력이 더 이상 예상된 힘을 생성할 수 없으므로 기존의 할당 방식은 유효하지 않게 된다. 따라서 제어기는 실제 남아 있는 액추에이터 유효성을 반영하는 고장 인지 할당(Fault-Aware Allocation)을 구성해야 한다.

모터 고장은 완전한 추력 상실, 추력 감소, 과도한 모터 저항, 인버터 고장, 프로펠러 손상, 모터 과열, 전기적 단선 또는 간헐적인 구동 동작 등 다양한 형태로 발생할 수 있다. 이러한 상태는 제어 가능성(Controllability)에 서로 다른 영향을 미친다. 부분적으로 성능이 저하된 모터는 여전히 유용한 추력을 제공할 수 있지만 완전히 고장 난 모터는 양의 추력을 전혀 제공하지 못하고 공력 항력(Aerodynamic Drag)을 발생시킬 수도 있다. 따라서 고장 분류(Fault Classification)는 단순히 고장 또는 정상 상태를 판단하는 것이 아니라 잔존 추력 능력을 추정해야 한다.

고장 탐지(Failure Detection)는 모터 명령, 전기 전류, 추정 추력, 회전 속도 및 기체 응답을 결합할 수 있다. 명령된 모터 속도가 증가했지만 예상되는 기체 가속도나 자세 응답이 발생하지 않는다면 모터 또는 프로펠러 고장을 의심할 수 있다. 예측된 항공기 동작과 실제 측정된 동작 사이의 차이를 이용하여 잔차(Residual)를 계산할 수 있다. 바람, 탑재물 변화, 급격한 기동 또는 센서 오류로 설명할 수 없는 지속적인 잔차는 실제 모터 고장의 강한 증거가 된다.

비행 제어기는 적극적인 재구성(Reconfiguration)을 수행하기 전에 고장 모터를 분리(Isolation)해야 한다. 센서 고장, 공력 외란, 액추에이터 포화 및 모터 고장은 단기간 동안 자세나 가속도에서 유사한 변화를 발생시킬 수 있다. 모터 속도, 전류, 명령 추력, IMU 측정값 및 추정 각가속도를 교차 검증하면 고장 분리 신뢰도를 향상시킬 수 있다. 영향을 받은 모터가 식별되면 제어 할당 모델에서 해당 모터의 사용 가능한 추력을 감소시키고 남아 있는 힘과 모멘트의 실행 가능한 범위를 계산할 수 있다.

단일 모터 고장의 중요한 결과는 독립적인 제어 권한의 상실이다. 일반적인 쿼드로터(Quadrotor)는 정상적으로 세 개의 회전축과 집합 추력(Collective Thrust)을 제어하기에 충분한 독립적인 4개의 액추에이터를 가진다. 하나의 모터를 제거하면 남아 있는 액추에이터만으로 모든 필요한 추력과 모멘트의 조합을 독립적으로 재현할 수 없기 때문에 완전한 공칭 4축 제어 목표를 유지하기 어려워진다. 따라서 제어 전략은 가장 중요한 상태를 우선시하고 덜 중요한 목표에서는 제어 성능의 저하를 허용해야 한다.

예를 들어 충분한 고도를 유지하고 롤과 피치를 제어하는 것을 공칭 요(Yaw) 권한을 유지하는 것보다 높은 우선순위로 설정할 수 있다. 모터 배치와 고장 위치에 따라 항공기는 피할 수 없는 잔여 모멘트로 인해 제어된 요 회전을 수행해야 할 수도 있다. 제어기는 남아 있는 모터 사이에서 추력을 재분배하여 이를 부분적으로 보상하면서 발생하는 회전 속도를 제한할 수 있다. 목표는 완벽한 자세를 유지하는 것이 아니라 항공기를 안정적이고 복구 가능한 비행 상태 안에 유지하는 것이다.

모터 고장 이후의 제어 할당(Control Allocation)은 제약 최적화 문제(Constrained Optimization Problem)로 구성할 수 있다. 최적화기는 원하는 총 추력과 모멘트, 정상 모터의 사용 가능한 추력 제한 및 고장 모터의 0 또는 감소된 제어 권한을 입력으로 받는다. 이후 최대 추력, 최소 추력, 모터 응답 제한, 배터리 전류 및 열적 제약조건을 만족하면서 실행 가능한 모터 명령을 계산한다. 우선순위 가중치(Priority Weight)를 이용하면 보조적인 궤적 목표보다 고도와 자세 안정성을 더 중요하게 처리할 수 있다.

액추에이터 집합이 감소하면 UAV의 비행 영역(Flight Envelope)도 변화한다. 모터 고장 이후 최대 상승률, 가속도, 기동성, 요 권한 및 허용 탑재물 중량이 감소할 수 있다. 외란을 억제할 수 있는 제어 여유가 감소하기 때문에 바람에 대한 내성도 낮아질 수 있다. 따라서 비행 제어 시스템은 항법 시스템이 정상 성능을 계속 요구하도록 허용하기보다 속도, 가속도, 각속도 및 명령 궤적 곡률에 대한 성능 저하 제한값을 설정해야 한다.

건전한 모터에 더 높은 추력이 요구될 수 있기 때문에 에너지 관리(Energy Management)가 더욱 중요해진다. 모터 부하가 증가하면 전류 소비와 열적 스트레스가 증가하고 배터리에서 더 큰 전압 강하가 발생할 수 있다. 따라서 고장 모터를 보상하기 위해 모든 정상 모터를 최대 추력으로 운전하는 제어기는 2차 전력 또는 열적 고장을 발생시킬 수 있다. 추력 재분배는 정상 모터가 추가적인 외란에 대응할 수 있도록 충분한 예비 여유(Reserve Margin)를 유지해야 한다.

모터 고장이 확인되면 UAV는 사전에 정의된 성능 저하 또는 비상 비행 모드(Degraded or Emergency Flight Mode)로 전환해야 한다. 항공기 구조와 임무 요구사항에 따라 전진 속도를 감소시키고, 기동을 제한하며, 보수적인 고도를 유지하고, 급격한 선회를 피하고, 복귀 또는 착륙 절차를 시작할 수 있다. 선택되는 대응은 잔존 제어 가능성, 착륙 가능 영역, 배터리 에너지, 바람 조건, 탑재물 및 사람이나 장애물과의 근접성을 고려해야 한다.

기존 임무 경로를 안전하게 유지할 수 없는 경우에는 궤적 재계획(Trajectory Replanning)이 필요하다. 결함 인지 플래너(Fault-Aware Planner)는 더 짧은 경로를 선택하거나 속도를 낮추고, 강한 바람 영역을 피하며, 장애물과의 안전 거리를 확대하거나 적절한 착륙 또는 복구 위치로 직접 이동할 수 있다. 자율 UAV에서는 이 결정이 비행 제어기와 임무 관리자(Mission Manager) 사이에서 조정되어야 하며, 항법 시스템이 성능 저하된 항공기의 능력을 초과하는 명령을 계속 생성하지 않도록 해야 한다.

서로 다른 비행 단계에서 발생하는 모터 고장은 서로 다른 위험 수준을 만든다. 호버링(Hovering) 중 고장이 발생하면 추력 균형이 즉시 변화하여 빠른 자세 편차가 발생할 수 있으며, 전진 비행 중 고장은 공력과 기존 제어 모멘트의 영향을 함께 받는다. 이륙, 착륙, 급격한 기동 또는 고고도 운전 중 발생하는 고장은 서로 다른 비상 대응을 필요로 할 수 있다. 따라서 검증에서는 하나의 고장 대응만 모든 비행 조건에 적용할 수 있다고 가정하지 않고 대표적인 비행 단계 전체에서 모터 고장을 평가해야 한다.

강건한 UAV 아키텍처는 초기 모터 고장 이후 발생할 수 있는 2차 고장(Second Failure) 가능성도 고려해야 한다. 하나의 모터가 고장 나면 남은 모터는 감소된 여유로 운전하게 되므로 추가적인 액추에이터 고장이 발생하면 복구가 불가능할 수 있다. 제어기는 잔존 추력 능력을 지속적으로 모니터링하고 계속 비행하는 것이 정당한지를 판단해야 한다. 남아 있는 제어 권한이 검증된 임계값 아래로 감소하면 시스템은 성능 저하 비행에서 비상 착륙 또는 기타 최소 위험 절차(Minimum-Risk Procedure)로 전환해야 한다.

모터 고장 사례의 검증(Verification)은 시뮬레이션(Simulation), 소프트웨어 인 더 루프(Software-in-the-Loop, SIL), 하드웨어 인 더 루프(Hardware-in-the-Loop, HIL) 및 통제된 실제 비행 시험을 결합해야 한다. 시험 시나리오에는 완전한 추력 상실, 부분 추력 감소, 모터 응답 지연, 간헐적인 동작, 모터 과열, 프로펠러 손상 및 전력 차단이 포함되어야 한다. 평가 항목에는 탐지 시간, 분리 정확도, 고도 편차, 자세 오차, 각속도 응답, 모터 사용률, 배터리 전류, 복구 시간 및 착륙 성능이 포함되어야 한다.

궁극적으로 UAV 모터 고장 사례는 고장 허용 제어가 액추에이터 고장을 관리 가능한 항공기 능력 감소로 전환하는 방법을 보여준다. 시스템은 비정상적인 모터 동작을 탐지하고 영향을 받은 액추에이터를 분리하며 잔존 추력 권한을 추정하고 사용 가능한 제어력을 재분배하며 비행 영역을 제한하고 성능 저하 비행 또는 비상 복구를 조정한다. 핵심 원칙은 사용할 수 없는 제어 권한을 요구하지 않고 검증된 제한 범위 내에서 남아 있는 액추에이터를 활용하면서 제어 가능한 비행이 지속 가능한지를 계속 평가하는 것이다.

##  

## 08.10 Fault Tolerant Control Certification: DO-178C

![](images/image10.png){width="7.268055555555556in" height="7.268055555555556in"}

Fault-tolerant control certification for airborne systems requires demonstrating that failure detection, isolation, reconfiguration, degraded operation, and recovery functions satisfy their assigned safety objectives. DO-178C provides a development assurance framework for airborne software, but it does not prescribe a specific fault-tolerant control architecture. The development process must therefore translate aircraft-level safety requirements into verifiable software requirements for monitoring, control allocation, mode transitions, and failure response.

The certification process begins with system safety assessment and functional hazard analysis. Potential failures such as sensor loss, actuator degradation, communication interruption, computational errors, and power-system abnormalities are analyzed according to their effects on aircraft behavior. The resulting safety classification determines the required development assurance level and influences the rigor applied to requirements, architecture, implementation, verification, configuration management, and quality assurance activities.

Fault-tolerant control functions should be decomposed into clearly defined software requirements. Requirements may specify fault detection thresholds, detection latency, fault isolation conditions, actuator reconfiguration logic, degraded control laws, mode-transition criteria, and safe-state behavior. Each requirement should be precise, deterministic, traceable, and testable. Ambiguous statements such as "maintain sufficient control" should be converted into measurable limits for attitude error, response time, actuator authority, speed, or other relevant parameters.

The software architecture should separate safety-critical monitoring, fault management, control computation, and non-critical optimization where practical. A fault-management function can supervise sensor validity, actuator availability, control-mode status, and residual capability while the control law performs the required vehicle stabilization. This separation supports verification and helps prevent uncertain diagnostic information or non-essential optimization functions from overriding independently established safety protections.

Independence and partitioning are important when fault-tolerant functions depend on redundant resources. If multiple software channels are intended to provide independent protection, common-mode failures must be considered. DO-178C objectives should be satisfied for each applicable software component, while system-level safety analysis demonstrates that the overall architecture achieves the required independence. Shared software, common data, common parameters, and common computational resources require particular attention.

Requirements-based testing should demonstrate that every safety-relevant fault response behaves as specified. Test cases should cover normal operation, fault detection, fault isolation, reconfiguration, degraded control, recovery, and safe-state transitions. Boundary conditions are particularly important because thresholds often determine when the system changes modes. Tests should demonstrate correct behavior immediately below, at, and above critical thresholds rather than validating only typical operating conditions.

Structural coverage provides additional evidence that implemented software has been adequately exercised. Depending on the assigned design assurance level, coverage objectives may include statement, decision, condition, modified condition/decision coverage, and related analysis as applicable. Fault-tolerant control software requires special attention because defensive branches, fault-handling paths, and degraded-mode logic may not execute during normal flight testing. Unexecuted code must be justified, removed, or adequately addressed according to the applicable certification objectives.

Model-based development and automatic code generation may be used for control algorithms when their development and verification processes satisfy applicable DO-178C objectives. Generated code should not be treated as automatically correct merely because the model has been validated. Requirements, model behavior, generated source code, object code, and verification evidence must maintain appropriate traceability. Tool qualification may also be required when a development or verification tool can eliminate or reduce otherwise necessary certification activities.

Fault injection is an important verification technique for fault-tolerant control. Simulated or hardware-injected sensor failures, actuator effectiveness reductions, communication faults, timing errors, and power limitations can demonstrate that the software enters the required control state. The injected condition, expected response, timing constraints, and pass criteria should be defined before execution. Results should be recorded as controlled verification evidence rather than informal demonstrations.

Timing and resource verification is essential because fault-tolerant control often operates within strict real-time constraints. Fault detection, isolation, reconfiguration, and control execution must complete within their allocated execution windows. Worst-case execution time, processor utilization, memory consumption, scheduling behavior, interrupt interactions, and communication latency should be evaluated. A function that produces the correct control decision after its required deadline may still represent a certification failure.

Robustness testing should evaluate invalid, abnormal, missing, delayed, and out-of-range inputs. Sensor data may become frozen, intermittent, corrupted, or inconsistent with other measurements. Actuator feedback may indicate impossible states or remain unchanged despite commands. The software should reject invalid information, prevent unsafe propagation of corrupted values, and transition according to defined requirements. Defensive programming must remain consistent with the verified architecture rather than introducing undocumented behavior.

Mode management requires particularly strong traceability because fault-tolerant systems frequently transition between nominal, degraded, recovery, and emergency states. Every transition should have defined entry conditions, exit conditions, priorities, reset behavior, and protection against unintended oscillation. Hysteresis or persistence logic may be required to prevent transient disturbances from repeatedly switching control modes. Verification should test both expected transitions and prohibited transitions under representative fault combinations.

Configuration management and change control are critical throughout certification. Fault-tolerant control parameters such as thresholds, actuator effectiveness limits, timing constants, control gains, and degraded-mode constraints can directly affect safety behavior. Each parameter should have controlled ownership, identification, configuration status, and verification evidence. Changes to safety-relevant software or parameters should trigger appropriate impact analysis and regression testing before being incorporated into the certified baseline.

Certification evidence should connect system safety objectives to aircraft requirements, software requirements, architecture, source code, tests, analyses, and results. Traceability should demonstrate that each applicable safety requirement is implemented and verified and that verification results correspond to the approved software baseline. Anomalies, deviations, unresolved issues, and derived requirements must be controlled and assessed for their potential impact on safety and certification.

Ultimately, DO-178C certification of fault-tolerant control is not achieved simply by demonstrating that a robot or aircraft continues flying after a simulated failure. Certification requires objective evidence that the software was systematically specified, designed, implemented, verified, configured, and controlled according to the applicable development assurance objectives. The fault-tolerant function must therefore be treated as part of an integrated safety architecture in which failure behavior, degraded control authority, timing, mode transitions, verification coverage, and recovery actions are all explicitly defined and supported by auditable evidence.

항공 시스템(Airborne System)의 고장 허용 제어(Fault-Tolerant Control) 인증은 고장 탐지(Failure Detection), 고장 분리(Isolation), 재구성(Reconfiguration), 성능 저하 운전(Degraded Operation) 및 복구(Recovery) 기능이 할당된 안전 목표(Safety Objective)를 충족한다는 것을 입증해야 한다. DO-178C는 항공 소프트웨어(Airborne Software)를 위한 개발 보증 프레임워크(Development Assurance Framework)를 제공하지만 특정 고장 허용 제어 아키텍처를 규정하지는 않는다. 따라서 개발 프로세스에서는 항공기 수준의 안전 요구사항을 모니터링, 제어 할당, 모드 전환 및 고장 대응을 위한 검증 가능한 소프트웨어 요구사항으로 변환해야 한다.

인증 프로세스는 시스템 안전성 평가(System Safety Assessment)와 기능 위험 분석(Functional Hazard Analysis)에서 시작한다. 센서 상실, 액추에이터 성능 저하, 통신 중단, 연산 오류 및 전력 시스템 이상과 같은 잠재적인 고장을 항공기 동작에 미치는 영향에 따라 분석한다. 그 결과로 결정되는 안전 분류(Safety Classification)는 필요한 개발 보증 수준(Development Assurance Level)을 결정하고 요구사항, 아키텍처, 구현, 검증, 형상 관리(Configuration Management) 및 품질 보증(Quality Assurance) 활동에 적용되는 엄격성을 결정한다.

고장 허용 제어 기능은 명확하게 정의된 소프트웨어 요구사항으로 분해되어야 한다. 요구사항에는 고장 탐지 임계값, 탐지 지연시간, 고장 분리 조건, 액추에이터 재구성 로직, 성능 저하 제어 법칙(Control Law), 모드 전환 조건 및 안전 상태 동작이 포함될 수 있다. 각각의 요구사항은 정확하고 결정론적이며 추적 가능하고 시험 가능해야 한다. "충분한 제어를 유지한다"와 같은 모호한 표현은 자세 오차, 응답 시간, 액추에이터 권한, 속도 또는 기타 관련 항목에 대한 측정 가능한 제한값으로 변환해야 한다.

소프트웨어 아키텍처는 가능한 경우 안전 중요 모니터링(Safety-Critical Monitoring), 고장 관리(Fault Management), 제어 계산(Control Computation) 및 비안전 중요 최적화(Non-Critical Optimization)를 명확하게 분리해야 한다. 고장 관리 기능은 센서 유효성, 액추에이터 가용성, 제어 모드 상태 및 잔존 능력(Residual Capability)을 감독하는 반면, 제어 법칙은 필요한 항공기 안정화를 수행할 수 있다. 이러한 분리는 검증을 지원하며 불확실한 진단 정보나 필수적이지 않은 최적화 기능이 독립적으로 설정된 안전 보호 기능을 무시하는 것을 방지하는 데 도움이 된다.

중복 자원(Redundant Resource)에 의존하는 고장 허용 기능에서는 독립성(Independence)과 파티셔닝(Partitioning)이 중요하다. 여러 소프트웨어 채널이 독립적인 보호 기능을 제공하도록 설계된 경우 공통 원인 고장(Common-Mode Failure)을 고려해야 한다. 각 적용 가능한 소프트웨어 구성요소에 대해 DO-178C의 목표를 충족해야 하며, 시스템 수준의 안전 분석을 통해 전체 아키텍처가 요구되는 독립성을 확보한다는 것을 입증해야 한다. 공유 소프트웨어, 공통 데이터, 공통 파라미터 및 공통 연산 자원에는 특히 주의가 필요하다.

요구사항 기반 시험(Requirements-Based Testing)은 모든 안전 관련 고장 대응이 정의된 사양에 따라 동작한다는 것을 입증해야 한다. 시험 사례는 정상 운전, 고장 탐지, 고장 분리, 재구성, 성능 저하 제어, 복구 및 안전 상태 전환을 포함해야 한다. 임계값이 모드 변경 시점을 결정하는 경우 경계 조건(Boundary Condition)이 특히 중요하다. 시험에서는 일반적인 운전 조건만 검증하는 것이 아니라 중요 임계값의 바로 아래, 정확히 해당하는 지점 및 바로 위에서 올바른 동작이 이루어지는지 입증해야 한다.

구조적 커버리지(Structural Coverage)는 구현된 소프트웨어가 충분히 실행되었다는 추가적인 증거를 제공한다. 할당된 설계 보증 수준에 따라 문장 커버리지(Statement Coverage), 결정 커버리지(Decision Coverage), 조건 커버리지(Condition Coverage), 수정 조건/결정 커버리지(Modified Condition/Decision Coverage) 및 관련 분석이 적용될 수 있다. 고장 허용 제어 소프트웨어에서는 방어적 분기(Defensive Branch), 고장 처리 경로 및 성능 저하 모드 로직이 정상 비행 시험 중에는 실행되지 않을 수 있으므로 특별한 주의가 필요하다. 실행되지 않은 코드는 적용 가능한 인증 목표에 따라 정당화하거나 제거하거나 적절하게 처리해야 한다.

모델 기반 개발(Model-Based Development)과 자동 코드 생성(Automatic Code Generation)은 해당 개발 및 검증 프로세스가 적용 가능한 DO-178C 목표를 충족하는 경우 제어 알고리즘에 사용할 수 있다. 생성된 코드는 모델이 검증되었다는 이유만으로 자동으로 올바른 것으로 간주해서는 안 된다. 요구사항, 모델 동작, 생성된 소스 코드, 오브젝트 코드 및 검증 증거는 적절한 추적성을 유지해야 한다. 개발 또는 검증 도구가 원래 필요했던 인증 활동을 제거하거나 감소시킬 수 있는 경우에는 도구 자격 인증(Tool Qualification)이 필요할 수도 있다.

고장 주입(Fault Injection)은 고장 허용 제어를 검증하는 중요한 기법이다. 센서 고장, 액추에이터 유효성 감소, 통신 고장, 타이밍 오류 및 전력 제한을 시뮬레이션하거나 하드웨어에 주입하여 소프트웨어가 요구된 제어 상태로 진입하는지를 입증할 수 있다. 주입 조건, 예상 응답, 시간 제약조건 및 합격 기준은 시험 실행 전에 정의해야 한다. 결과는 비공식적인 시연이 아니라 통제된 검증 증거(Verification Evidence)로 기록해야 한다.

고장 허용 제어는 일반적으로 엄격한 실시간 제약조건(Real-Time Constraint) 안에서 동작하기 때문에 시간 및 자원 검증(Timing and Resource Verification)이 필수적이다. 고장 탐지, 분리, 재구성 및 제어 실행은 할당된 실행 시간 내에 완료되어야 한다. 최악 실행 시간(Worst-Case Execution Time), 프로세서 사용률, 메모리 소비량, 스케줄링 동작, 인터럽트 상호작용 및 통신 지연시간을 평가해야 한다. 올바른 제어 결정을 생성하더라도 요구된 시간 제한 이후에 결과가 생성된다면 인증 관점에서는 실패가 될 수 있다.

강건성 시험(Robustness Testing)은 유효하지 않거나 비정상적이거나 누락되거나 지연되거나 범위를 벗어난 입력을 평가해야 한다. 센서 데이터는 고정되거나 간헐적으로 발생하거나 손상되거나 다른 측정값과 일치하지 않을 수 있다. 액추에이터 피드백은 물리적으로 불가능한 상태를 나타내거나 명령을 전달했음에도 변하지 않을 수 있다. 소프트웨어는 유효하지 않은 정보를 거부하고 손상된 값이 안전하지 않은 방식으로 전파되는 것을 방지하며 정의된 요구사항에 따라 전환해야 한다. 방어적 프로그래밍(Defensive Programming)은 검증된 아키텍처와 일관성을 유지해야 하며 문서화되지 않은 동작을 새롭게 추가해서는 안 된다.

모드 관리(Mode Management)는 고장 허용 시스템이 정상, 성능 저하, 복구 및 비상 상태 사이에서 자주 전환하기 때문에 특히 강력한 추적성이 필요하다. 각각의 전환에는 정의된 진입 조건, 종료 조건, 우선순위, 리셋 동작 및 의도하지 않은 반복 전환을 방지하기 위한 보호 기능이 있어야 한다. 일시적인 외란으로 인해 제어 모드가 반복적으로 전환되는 것을 방지하기 위해 히스테리시스(Hysteresis) 또는 지속성 로직(Persistence Logic)이 필요할 수 있다. 검증에서는 예상되는 전환뿐만 아니라 대표적인 고장 조합 조건에서 금지된 전환도 시험해야 한다.

인증 전 과정에서 형상 관리(Configuration Management)와 변경 관리(Change Control)는 매우 중요하다. 고장 허용 제어 파라미터에는 임계값, 액추에이터 유효성 한계, 타이밍 상수, 제어 이득 및 성능 저하 모드 제약조건 등이 포함될 수 있으며, 이러한 값들은 안전 동작에 직접적인 영향을 미칠 수 있다. 각각의 파라미터는 관리 책임, 식별 정보, 형상 상태 및 검증 증거를 명확하게 가져야 한다. 안전 관련 소프트웨어 또는 파라미터를 변경할 경우에는 인증 기준선(Certified Baseline)에 반영하기 전에 적절한 영향 분석(Impact Analysis)과 회귀 시험(Regression Testing)을 수행해야 한다.

인증 증거(Certification Evidence)는 시스템 안전 목표에서 항공기 요구사항, 소프트웨어 요구사항, 아키텍처, 소스 코드, 시험, 분석 및 결과까지 연결되어야 한다. 추적성(Traceability)은 적용 가능한 각 안전 요구사항이 구현되고 검증되었으며 검증 결과가 승인된 소프트웨어 기준선과 일치한다는 것을 입증해야 한다. 이상 현상(Anomaly), 편차(Deviation), 미해결 문제 및 파생 요구사항(Derived Requirement)은 관리되어야 하며 안전과 인증에 미치는 잠재적 영향을 평가해야 한다.

궁극적으로 DO-178C에 따른 고장 허용 제어 인증은 단순히 시뮬레이션된 고장 이후 로봇이나 항공기가 계속 비행할 수 있음을 보여주는 것만으로 달성되지 않는다. 인증을 위해서는 소프트웨어가 적용 가능한 개발 보증 목표에 따라 체계적으로 정의되고, 설계되고, 구현되고, 검증되고, 형상 관리되며 통제되었다는 객관적인 증거가 필요하다. 따라서 고장 허용 기능은 통합된 안전 아키텍처(Integrated Safety Architecture)의 일부로 취급해야 하며, 여기에는 고장 동작, 성능 저하된 제어 권한, 타이밍, 모드 전환, 검증 커버리지 및 복구 동작이 모두 명시적으로 정의되고 감사 가능한 증거(Auditable Evidence)에 의해 뒷받침되어야 한다.
