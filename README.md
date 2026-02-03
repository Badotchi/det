flowchart TD
    Start([START]) --> Setup[Setup Phase]
    
    Setup --> InitHardware[Initialize Hardware:<br/>- Serial Communication 9600 baud<br/>- Attach servos to pins 16 & 17<br/>- Configure joystick button pin 14<br/>- Configure LED pins 15,22,4,5<br/>- Set all LEDs LOW<br/>- Set servos to 90° center]
    
    InitHardware --> Loop([MAIN LOOP])
    
    Loop --> UpdateButton[Update button state<br/>button.loop]
    UpdateButton --> GetTime[Get current time<br/>currentMillis = millis]
    
    GetTime --> CheckButton{Button<br/>Pressed?}
    
    CheckButton -->|Yes| ToggleHazard[Toggle hazard mode<br/>hazardMode = !hazardMode]
    ToggleHazard --> HazardOff{Hazard<br/>turned OFF?}
    HazardOff -->|Yes| ClearLEDs[Turn all LEDs OFF]
    HazardOff -->|No| ReadJoystick
    ClearLEDs --> ReadJoystick
    
    CheckButton -->|No| ReadJoystick[Read Joystick X-axis:<br/>Average 5 samples<br/>xValue = readJoystickX]
    
    ReadJoystick --> InHazard{Hazard Mode<br/>Active?}
    
    InHazard -->|Yes| CheckBlink1{Time for<br/>blink update?<br/>≥300ms}
    CheckBlink1 -->|Yes| BlinkAll[Toggle all 4 LEDs<br/>hazardLedState = !hazardLedState]
    CheckBlink1 -->|No| SetCenter1
    BlinkAll --> SetCenter1[Set both servos to 90°<br/>center position]
    SetCenter1 --> Debug
    
    InHazard -->|No| CheckJoyUp{xValue <<br/>1648?<br/>Joystick UP}
    
    CheckJoyUp -->|Yes| CheckBlink2{Time for<br/>blink update?<br/>≥300ms}
    CheckBlink2 -->|Yes| BlinkLeft[Toggle LEFT LEDs<br/>leftLedState = !leftLedState]
    CheckBlink2 -->|No| ServoRight
    BlinkLeft --> ServoRight[Move servos RIGHT:<br/>Both servos to 150°]
    ServoRight --> RightOff[Turn RIGHT LEDs OFF]
    RightOff --> Debug
    
    CheckJoyUp -->|No| CheckJoyDown{xValue ><br/>2448?<br/>Joystick DOWN}
    
    CheckJoyDown -->|Yes| CheckBlink3{Time for<br/>blink update?<br/>≥300ms}
    CheckBlink3 -->|Yes| BlinkRight[Toggle RIGHT LEDs<br/>rightLedState = !rightLedState]
    CheckBlink3 -->|No| ServoLeft
    BlinkRight --> ServoLeft[Move servos LEFT:<br/>Both servos to 30°]
    ServoLeft --> LeftOff[Turn LEFT LEDs OFF]
    LeftOff --> Debug
    
    CheckJoyDown -->|No| Centered[CENTERED:<br/>Both servos to 90°<br/>All LEDs OFF<br/>Reset LED states]
    Centered --> Debug
    
    Debug --> CheckDebug{Time for<br/>debug print?<br/>≥1000ms}
    CheckDebug -->|Yes| PrintDebug[Print to Serial:<br/>- Joystick value<br/>- Left servo position<br/>- Right servo position]
    CheckDebug -->|No| Loop
    PrintDebug --> Loop
    
    style Start fill:#90EE90
    style Loop fill:#87CEEB
    style InHazard fill:#FFD700
    style CheckJoyUp fill:#FFD700
    style CheckJoyDown fill:#FFD700
    style BlinkAll fill:#FF6B6B
    style BlinkLeft fill:#FF6B6B
    style BlinkRight fill:#FF6B6B
    style ServoRight fill:#9370DB
    style ServoLeft fill:#9370DB
    style SetCenter1 fill:#9370DB
    style Centered fill:#9370DB
    style Debug fill:#DEB887
