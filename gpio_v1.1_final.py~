#!/usr/bin/python
'''
Notes
-----
v1.0
Pin numbering(BCM):This is a lower level way of working - it refers to the channel numbers on the Broadcom SOC. 
You have to always work with a diagram of which channel number goes to which pin on the RPi board. 
Your script could break between revisions of Raspberry Pi boards.
'''

#call libraries
try:
    import RPi.GPIO as io
except RuntimeError:
    print("Error importing RPi.GPIO! are you su or sudo?")

import smtplib
import mimetypes
import email.mime.application
import datetime
import time
import pygame
import pygame.camera
from pygame.locals import *

#set gpio variables
io.setmode(io.BCM)										
pir_pin = 18					    #motion sensor on breadboard to header channel
door_pin = 23					    #door magnet on breadboard to header channel
io.setup(pir_pin, io.IN)			    # (channel, input) - sets specific channel as input
io.setup(door_pin, io.IN, pull_up_down=io.PUD_UP)   #input pin not connected to anything until switch is activated: use pulldown (software) resistor to set default value to kill floating noise data

#set time variables
get_day = datetime.datetime.today().weekday()
get_hour = datetime.datetime.now().time().hour
get_minute = datetime.datetime.now().time().minute
get_timestamp = datetime.datetime.now().strftime('%b-%d-%y')

#set email variables - consider changing lazy obfuscation
TO = 'nexus06@gmail.com'
GMAIL_USER = 'nexus06@gmail.com'
GMAIL_PASS = 'y0ur3nc0d3dPw'.decode('base64') 
SUBJECT = 'Unknown Event Trigger:'
body = email.mime.Text.MIMEText('Your PIR sensor detected movement')

#MIME message construct
msg = email.mime.Multipart.MIMEMultipart()
msg['Subject'] = 'PIR movement detected!'
msg['From'] = 'nexus06@gmail.com'
msg['To'] = 'nexus06@gmail.com'
msg.attach(body)

#Functions
#Generate & Send E-mail
def send_gmail():
    #generate MIME attachment
    print("Sending Email...")
    filename = 'img_' +str(get_timestamp)+'.jpg' 
    fp = open(filename, 'rb')
    att = email.mime.application.MIMEApplication(fp.read(),_subtype="jpeg")
    fp.close
    att.add_header('Content-Disposition','attachment',filename=filename)
    msg.attach(att)
    #send e-mail
    smtpserver = smtplib.SMTP("smtp.gmail.com",587) 
    smtpserver.ehlo()
    smtpserver.starttls()
    smtpserver.ehlo
    smtpserver.login(GMAIL_USER, GMAIL_PASS)
    smtpserver.sendmail(GMAIL_USER, TO, msg.as_string())
    smtpserver.close()

#take picture
def take_picture():
	cam = pygame.camera.Camera("/dev/video0",(320,240))	#1280x960 max but slow
	pygame.init()
	cam.start()
	print "Capturing Image..."
	image_output = cam.get_image()
        pygame.image.save(image_output,'img_' +str(get_timestamp)+'.jpg')
        cam.stop()

#activate PIR
def pir_init():
    while True:
		m = datetime.datetime.now().time().minute	#loop through for current time
		h = datetime.datetime.now().time().hour
		d = datetime.datetime.today().weekday()
		if d > 4:
			print("Sleeping...")
			print time_sleep
			time_sleep = (((7 - d) * 3600) * 24) - (h * 3600) + (9 * 3600)
			time.sleep(time_sleep)
		elif h < 9:	                                        
			print("Sleeping...")
			time_sleep = (9 - h) * 3600
			time.sleep(time_sleep)
		elif h > 16:
			print("Sleeping...")
			time_sleep = ((24 - h) + 9) * 3600
			time.sleep(time_sleep)
		elif io.input(pir_pin):
			print("PIR ALARM")
			pygame.camera.init()
			time.sleep(1.5)
			take_picture()
			send_gmail()   
			time.sleep(10)      #cooldown to prevent email spew
		else:
			print "Waiting..."
			time.sleep(0.5)     #sleep and wait for movement
			print m
    print "Wait"
    

# main
if get_day == 0 or get_day < 5:               #Weekdays
    if get_hour > 8 and get_hour < 17:       #Hour Range
        pir_init()
    elif get_hour > 16:
        print("Initialization at 9am...")
	time_sleep = ((24 - get_hour) + 9) * 3600
        time.sleep(time_sleep)
        pir_init()              #initiate sensor
    elif get_hour < 9:
        print("Initialization at 9am...")
        time_sleep = (9 - get_hour) * 3600
        time.sleep(time_sleep)
        pir_init()
else:
    time_sleep = (((7 - get_day) * 3600) * 24) - (get_hour * 3600) + (9 * 3600)
    print "Sleeping..."
    print time_sleep
    time.sleep(time_sleep)
    pir_init()

    

    
	

 	
