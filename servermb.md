# TPRG Final project
# Student Name : Manpreet Badgal
# Student Id : 100945948
# submission Date : 13 December 2024 
# This program is strictly my own work. Any material
# beyond course learning materials that is taken from
# the Web or other sources is properly cited, giving.
# credit to the original author(s)



import socket
import json
import PySimpleGUI as sg
import subprocess

def start_server(host='', port=5000):
    """Start the server to listen for client connections and handle incoming data."""
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.bind((host, port))
    server_socket.listen(1)

   
    client_socket, client_address = server_socket.accept()
    print(f"Connected to {client_address}")

    return client_socket
