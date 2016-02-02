#!/usr/bin/env python
# coding: utf-8
# Copyright 2016 Lixin Jin, Abram Hindle, https://github.com/tywtyw2002, and https://github.com/treedust
# 
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
# 
#     http://www.apache.org/licenses/LICENSE-2.0
# 
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Do not use urllib's HTTP GET and POST mechanisms.
# Write your own HTTP GET and POST
# The point is to understand what you have to send and get experience with it

import sys
import socket
import re
# you may use urllib to encode data appropriately
import urllib

def help():
    print "httpclient.py [GET/POST] [URL]\n"

class HTTPResponse(object):
    def __init__(self, code=200, body=""):
        self.code = code
        self.body = body

class HTTPClient(object):

    def connect(self, host, port):
	if (port == None):
		port = 80
        # create a socket connection.
	try:
		clientSocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
		clientSocket.connect((host, port))
	except:
		sys.exit()
        return clientSocket

    def get_code(self, data):
	if data == None:
		return 500
	modifiedData = data.split(" ")
	code = int(modifiedData[1])
        return code

    def get_body(self, data):
	if data == None:
		return ""
	modifiedData = data.split("\r\n\r\n")
	body = modifiedData[1] + "\r\n"
        return body

    # read everything from the socket
    def recvall(self, sock):
        buffer = bytearray()
        done = False
        while not done:
            part = sock.recv(1024)
            if (part):
                buffer.extend(part)
            else:
                done = not part
        return str(buffer)

    def get_host_port(self, uri):
	if (":" in uri[0]):
		modify = uri[0].split(":")
		hostPort = modify[1]
	else:
		hostPort = 80
	return int(hostPort)

    def get_host_path(self, uri):
	if (len(uri) == 2):
		# get rid of query
		if ("?" not in uri[1]):
			hostPath = "/" + uri[1]
		else:
			modify = uri[1].split("?")
			hostPath = "/" + modify[0]
	else:
		hostPath = ""
	return hostPath	

    def get_host_name(self, uri):
	if (":" in uri[0]):
		modify = uri[0].split(":")
		hostName = modify[0]
	else:
		hostName = uri[0]
	return hostName

    def set_GET_header(self, path, host):
	header = "GET " + path + " HTTP/1.1\r\n" 
	header = header + "Host: " + host +"\r\n"
	header = header + "Connection: close\r\n"
	header = header + "Accept: */*\r\n\r\n"
	return header

    def set_POST_header(self, path, host, args):
	header = "POST " + path + " HTTP/1.1\r\n" 
	header = header + "Host: " + host +"\r\n"
	header = header + "Connection: close\r\n"
	header = header + "Accept: */*\r\n"
 	header = header + "Content-Type: application/x-www-form-urlencoded\r\n"
        header = header +"Content-length: "+ str(len(args)) + "\r\n\r\n" 
        header = header + args
	return header


    def GET(self, url, args=None):
	uri = re.split("://", url)[1]
	uri = uri.split('/', 1)

	port = self.get_host_port(uri)
	path = self.get_host_path(uri)
	host = self.get_host_name(uri)

	header = self.set_GET_header(path, host)
	clientSocket = self.connect(host, port)
	clientSocket.sendall(header)

	response = self.recvall(clientSocket)	
	code = self.get_code(response)
	body = self.get_body(response)
	
        return HTTPResponse(code, body)

    def POST(self, url, args=None):
	uri = re.split("://", url)[1]
	uri = uri.split('/', 1)

	port = self.get_host_port(uri)
	path = self.get_host_path(uri)
	host = self.get_host_name(uri)
	
	if type(args) is dict:
		modifiedArg = urllib.urlencode(args)
	else:
		modifiedArg = ""

	header = self.set_POST_header(path, host, modifiedArg)
	clientSocket = self.connect(host, port)
	clientSocket.sendall(header)

	response = self.recvall(clientSocket)
	code = self.get_code(response)
	body = self.get_body(response)

        return HTTPResponse(code, body)

    def command(self, url, command="GET", args=None):
        if (command == "POST"):
            return self.POST( url, args )
        else:
            return self.GET( url, args )
    
if __name__ == "__main__":
    client = HTTPClient()
    command = "GET"
    if (len(sys.argv) <= 1):
        help()
        sys.exit(1)
    elif (len(sys.argv) == 3):
        print client.command( sys.argv[2], sys.argv[1] )
    else:
        print client.command( sys.argv[1] )   
