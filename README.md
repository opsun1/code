import numpy as np
import cv2

def load_image(path_img):
    return cv2.imread(path_img)

def bgr2hsv(img):
    return cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

def setRangeColor(hsv, lower_color, upper_color):
    return cv2.inRange(hsv, lower_color, upper_color)

def contours_img(mask):
    contours,_ = cv2.findContours(mask, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
    return contours

def filter_contours_img(contours, img_draw, color_bbox):
    count = 0
    for c in contours:
        rect = cv2.boundingRect(c)
        x,y,w,h = rect
        area = w * h

        if area > 1000:
            count = count + 1
            cv2.rectangle(img_draw, (x, y), (x+w, y+h), color_bbox, 5)
    return img_draw, count

def draw_text_on_image(img_draw, count_Red, count_Blue, count_Green):
    cv2.putText(img_draw,'Red Count : ' + str(count_Red), 
        (10,230),                  # bottomLeftCornerOfText
        cv2.FONT_HERSHEY_SIMPLEX, # font 
        0.5,                      # fontScale
        (0,0,0),            # fontColor
        2)                        # lineType

    cv2.putText(img_draw,'Blue Count : ' + str(count_Blue), 
        (10,260),                  # bottomLeftCornerOfText
        cv2.FONT_HERSHEY_SIMPLEX, # font 
        0.5,                      # fontScale
        (0,0,0),            # fontColor
        2)                        # lineType
    cv2.putText(img_draw,'Green Count : ' + str(count_Green), 
        (10,290),                  # bottomLeftCornerOfText
        cv2.FONT_HERSHEY_SIMPLEX, # font 
        0.5,                      # fontScale
        (0,0,0),            # fontColor
        2)                        # lineType
    return img_draw

def main():
    path_img = 'E:\\New folder\\ss\\image.jpg'
    img = load_image(path_img)
    img = cv2.resize(img, None,fx=0.5,fy=0.5)
    hsv = bgr2hsv(img)
    img_draw = img

    lower_Red = np.array([160, 100, 100])
    upper_Red = np.array([179, 255, 255])
    mask = setRangeColor(hsv, lower_Red, upper_Red)
    contours = contours_img(mask)
    color_rbox = (0, 0, 255)
    img_draw, count_Red = filter_contours_img(contours, img_draw, color_rbox)
    print('Red Count:', count_Red)

    lower_Green = np.array([50,80, 40])
    upper_Green = np.array([70, 255, 255])
    mask = setRangeColor(hsv, lower_Green, upper_Green)
    contours = contours_img(mask)
    color_gbox = (0, 255, 0)
    img_draw, count_Green = filter_contours_img(contours, img_draw, color_gbox)
    print('Green Count:', count_Green)

    lower_Blue = np.array([94, 127, 100])
    upper_Blue = np.array([120, 255, 255])
    mask = setRangeColor(hsv, lower_Blue, upper_Blue)
    contours = contours_img(mask)
    color_bbox = (255, 0, 0)
    img_draw, count_Blue = filter_contours_img(contours, img_draw, color_bbox)
    print('Blue Count:', count_Blue)

    img_draw = draw_text_on_image(img_draw, count_Red, count_Blue, count_Green)

    cv2.imwrite('E:\\New folder\\s\\output.image.jpg', img_draw)
    
main()
