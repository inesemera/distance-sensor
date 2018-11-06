# distance-sensor
This is touch-less light switch using ultrasonic sensor on raspberry pi
import RPi.GPIO as GPIO
import time

TRIG = 7
ECHO = 11
relay = 12

print ("Distance Measurement In Progress")

def dist():
	GPIO.setmode(GPIO.BOARD)
	GPIO.setwarnings(False)
	GPIO.setup(TRIG, GPIO.OUT)
	GPIO.setup(ECHO, GPIO.IN)
	GPIO.output(TRIG, False)
	
	#print "waiting for sensor to settle"
	
	time.sleep(1)
	GPIO.output(TRIG, True)
	time.sleep(0.00001)
	GPIO.output(TRIG, False)
	while GPIO.input(ECHO)==0:
			pulse_start = time.time()
	while GPIO.input(ECHO)==1:
			pulse_end = time.time()
	
	pulse_duration = pulse_end - pulse_start
	
	#multiply with the sonic speed (34300 cm/s)
	#and divide by 2, because there and back
	
	global distance
	
	distance = (pulse_duration * 34300) / 2
	distance = round(distance, 2)
	
def relayON():
	GPIO.setmode(GPIO.BOARD)
	GPIO.setwarnings(False)
	GPIO.setup(relay, GPIO.OUT)
	GPIO.output(relay, GPIO.LOW)
	f = open("iot/status.txt", "w")
	f.write("ON")
	f.close()
	
def relayOFF():
	GPIO.setmode(GPIO.BOARD)
	GPIO.setwarnings(False)
	GPIO.setup(relay, GPIO.OUT)
	GPIO.output(relay, GPIO.HIGH)
	f = open("iot/status.txt", "w")
	f.write("OFF")
	f.close()
	
while (True):
	dist()	
	if (distance<20):
            print ("Distance", distance, "cm")
            f = open("iot/status.txt", "r")
            status=f.read()
            if (status=="ON"):
                relayOFF()
                GPIO.cleanup()
            elif (status=="OFF"):
                relayON()
GPIO.cleanup()
