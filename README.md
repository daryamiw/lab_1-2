# lab_8

import cv2
import argparse

def highlightFace(net, frame, conf_threshold=0.6):
    frameOpencvDnn = frame.copy()

    frameHeight = frameOpencvDnn.shape[0]
    frameWidth = frameOpencvDnn.shape[1]

    blob = cv2.dnn.blobFromImage(frameOpencvDnn, 1.0, (300, 300), [104, 117, 123], True, False)
    net.setInput(blob)
    detections = net.forward()
    faceBoxes = []

    for i in range(detections.shape[2]):
        confidence = detections[0, 0, i, 2]
        if confidence > conf_threshold:
            x1 = int(detections[0, 0, i, 3] * frameWidth)
            y1 = int(detections[0, 0, i, 4] * frameHeight)

            x2 = int(detections[0, 0, i, 5] * frameWidth)
            y2 = int(detections[0, 0, i, 6] * frameHeight)

            faceBoxes.append([x1, y1, x2, y2])

            cv2.rectangle(frameOpencvDnn, (x1, y1), (x2, y2), (0, 225, 0), int(round(frameHeight / 150)), 8)

    return frameOpencvDnn, faceBoxes

# подключаем парсер аргументов командной строки
parser=argparse.ArgumentParser()
# добавляем аргумент для работы с изображениями
parser.add_argument('--image')
# сохраняем аргументы в отдельную переменную
args=parser.parse_args()

video=cv2.VideoCapture(0)

faceProto="opencv_face_detector.pbtxt"
faceModel="opencv_face_detector_uint8.pb"
faceNet=cv2.dnn.readNet(faceModel,faceProto)


while cv2.waitKey (1) < 0:
    hasFrame, frame = video.read()

    if not hasFrame:
        cv2.waitKey
        break

    resultImg, faceBoxes = highlightFace(faceNet, frame)

    if not faceBoxes:
        print('Лицо не распознано')

    cv2.imshow('face recognition', resultImg)
