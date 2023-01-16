# Conclusion
### Purpose
    To output the signal when detecting potholes or speed bumps
<image src="https://user-images.githubusercontent.com/95682287/212618151-a25a5d20-ec20-47a0-b813-0de6e9639dbe.gif" width="80%" height="80%"></image>
# Algorithm
    Using YOLOv3 & Alexeyab's darknet with custom dataset
||Original|with Algorithm
|------|---|---|
|potholes|![prc3](https://user-images.githubusercontent.com/95682287/212258118-395426f5-7b7a-4014-90b9-feba5088ac72.jpg)|![그림1](https://user-images.githubusercontent.com/95682287/212258127-c6266053-03a7-4a7d-b907-66be94dece25.png)|
|speed bumps|![image](https://user-images.githubusercontent.com/95682287/212258299-d47673b0-906e-4ac9-8566-744e019d782a.png)|![image](https://user-images.githubusercontent.com/95682287/212257554-3cb41b3f-7ac6-42d3-8ebd-1ecbe6ce2f63.png)|

### Amount of Dataset
  - potholes : 3,968 img files(.jpg)
  - speed bumps : 3,579 img files(.jpg)
  - total : 7,547 img files(.jpg)
# Code Modification
    After detected with deep learning, a predefined signal is sent to another board connected to UART
### Add definition UART
    #include <stdio.h>
    #include <unistd.h>       // Used for UART
    #include <sys/fcntl.h>    // Used for UART
    #include <termios.h>      // Used for UART
    #define     NSERIAL_CHAR   256
    #define     VMINX          1
    #define     BAUDRATE       B115200
### Add output signal
    for (j = 0; j < classes; ++j) {
        int show = strncmp(names[j], "dont_show", 9);
        if (dets[i].prob[j] > thresh && show) {
            if (class_id < 0) {
                strcat(labelstr, names[j]);
                class_id = j;
                char buff[20];
                if (dets[i].track_id) {
                    sprintf(buff, " (id: %d)", dets[i].track_id);
                    strcat(labelstr, buff);
                }
                sprintf(buff, " (%2.0f%%)", dets[i].prob[j] * 100);
                strcat(labelstr, buff);
                printf("%s: %.0f%% detect object!! ", names[j], dets[i].prob[j] * 100);
                result = 0;
                if (strncmp(names[j], "pothole", 9) == 0) {
                    result = result | 0x02;  // 0000 0010
                    printf("class name : pothole.\n");
                }
                else if (strncmp(names[j], "bump", 9) == 0) {
                    printf("class name : speed_bump.\n");
                    result = result | 0x01;  // 0000 0001
                }
                sprintf(buffer, "$%d#", result);
                u.sendUart(buffer, strlen(buffer));
                if (dets[i].track_id) printf("(track = %d, sim = %f) ", dets[i].track_id, dets[i].sim);
            }
            else {
                strcat(labelstr, ", ");
                strcat(labelstr, names[j]);
                printf(", %s: %.0f%% ", names[j], dets[i].prob[j] * 100);
            }
        }
    }
# Tools & Environment
|Board|Camera||OS|Algorithm|
|------|---|---|---|---|
|<image src="https://user-images.githubusercontent.com/95682287/212259117-18ee7da9-286b-419e-b382-c2d005267e08.png" width="250px" height="50%">|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<image src="https://user-images.githubusercontent.com/95682287/212260296-59a5d3f4-25b0-4983-b01f-13ae473ea0f4.png" width="100px" height="300px" display="block">|&nbsp;&nbsp;&nbsp;&nbsp;&&nbsp;&nbsp;&nbsp;&nbsp;|<image src="https://user-images.githubusercontent.com/95682287/212258548-ebbaf589-144b-425d-a449-3ac2aa1a3941.png" width="200px" height="50%">|<image src="https://user-images.githubusercontent.com/95682287/212258451-7a96f83f-e782-41d9-b764-0a3d2a994c42.png" width="200px" height="50%">|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;NVIDIA Jetson Nano B01|Raspberry Camera V2||&nbsp;&nbsp;&nbsp;&nbsp;Linux Ubuntu 20.04 LTS|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;yolov3 & darknet
# Action Process
![image](https://user-images.githubusercontent.com/95682287/212267443-3c740fcf-d69a-4e0a-b1d2-3efb03a84aca.png)
# Flowchart & File Structure
<div>
<image src="https://user-images.githubusercontent.com/95682287/212267610-9923fa47-2c26-484d-a12b-821fe1625f1e.png" width="40%" height="40%"></image>
<image src="https://user-images.githubusercontent.com/95682287/212267511-fbbe903a-e200-40c1-9c62-edc3e79e3e9a.png" width="50%" height="50%"></image>
</div>
