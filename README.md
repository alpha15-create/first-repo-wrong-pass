# first-repo-wrong-pass
This is the alert alarm 
import pygame
import cv2
import threading
import time
import os
import pythoncom
from ctypes import cast, POINTER
from comtypes import CLSCTX_ALL
from pycaw.pycaw import AudioUtilities, IAudioEndpointVolume

# -------- FORCE VOLUME --------
def force_max_volume():
    import pythoncom
    pythoncom.CoInitialize()

    from ctypes import cast, POINTER
    from comtypes import CLSCTX_ALL
    from pycaw.pycaw import AudioUtilities, IAudioEndpointVolume

    devices = AudioUtilities.GetSpeakers()
    interface = devices.Activate(
        IAudioEndpointVolume._iid_,
        CLSCTX_ALL,
        None
    )
    volume = cast(interface, POINTER(IAudioEndpointVolume))

    while True:
        try:
            volume.SetMute(0, None)  # 🔊 unmute
            volume.SetMasterVolumeLevelScalar(1.0, None)  # 🔊 max
        except:
            pass

        time.sleep(0.1)  # faster forcing
# -------- SOUND --------
def play_sound():
    pygame.init()
    pygame.mixer.init()
    pygame.mixer.music.load("frog.mp3")
    pygame.mixer.music.set_volume(1.0)
    pygame.mixer.music.play(-1)

# -------- VIDEO --------
def play_video():
    cap = cv2.VideoCapture("f.mp4")

    if not cap.isOpened():
        print(" Video not found!")
        return

    cv2.namedWindow("ALERT", cv2.WND_PROP_FULLSCREEN)
    cv2.setWindowProperty("ALERT", cv2.WND_PROP_FULLSCREEN, cv2.WINDOW_FULLSCREEN)

    while True:
        ret, frame = cap.read()

        if not ret:
            cap.set(cv2.CAP_PROP_POS_FRAMES, 0)
            continue

        cv2.imshow("ALERT", frame)

        # ONLY EXIT KEY
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

    cap.release()
    cv2.destroyAllWindows()
    pygame.mixer.music.stop()

# -------- MAIN --------
correct_password = "1234"
user_password = input("Enter password: ")

if user_password != correct_password:
    print("❌ Wrong Password! ALERT ACTIVATED 🚨")

    # start threads
    threading.Thread(target=force_max_volume, daemon=True).start()
    threading.Thread(target=play_sound, daemon=True).start()

    play_video()

else:
    print(" Access Granted")
