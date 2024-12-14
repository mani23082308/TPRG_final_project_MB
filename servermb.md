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
def main():
    """Main function for the server to receive data from the client and display it in the GUI."""
    client_socket = start_server()

    # PySimpleGUI layout for displaying data
    layout = [
        [sg.Text("Server Connection Status:"), sg.Text("Disconnected", key="-STATUS-", text_color="red")],
        [sg.Text("Core Temperature (°C):", size=(20, 1)), sg.Text("", key="-CORE_TEMP-", size=(10, 1))],
        [sg.Text("GPU Memory (MB):", size=(20, 1)), sg.Text("", key="-GPU_MEM-", size=(10, 1))],
        [sg.Text("CPU Memory (MB):", size=(20, 1)), sg.Text("", key="-CPU_MEM-", size=(10, 1))],
        [sg.Text("SD Card Clock Speed (Hz):", size=(20, 1)), sg.Text("", key="-SD_CARD_CLK-", size=(10, 1))],
        [sg.Text("Pixel Clock Speed (Hz):", size=(20, 1)), sg.Text("", key="-PIXEL_CLK-", size=(10, 1))],
        [sg.Text("Clock Frequency (MHz):", size=(20, 1)), sg.Text("", key="-CLOCK_FREQ-", size=(10, 1))],
        [sg.Text("Iteration Count:", size=(20, 1)), sg.Text("", key="-ITER_COUNT-", size=(10, 1))],
        [sg.Text("LED Status:", size=(20, 1)), sg.Text("\u25CB", key="-LED-", size=(8, 1), text_color="#FF0000")],  # Red LED (initially off)
        [sg.Button("Exit")]
    ]

    window = sg.Window("Pi Metrics Server", layout, finalize=True, background_color='orangered')  #  background color

    led_on = False  # Initially LED is off


    while True:
        event, values = window.read(timeout=1000)

        if event == sg.WINDOW_CLOSED or event == "Exit":
            break

        try:
            data = client_socket.recv(1024).decode('utf-8')

            if not data:
                break  # No data received, close connection

            metrics = json.loads(data)

            # Update the GUI with new data
            window["-STATUS-"].update("Connected", text_color="blue")
            window["-CORE_TEMP-"].update(f"{metrics['Core Temperature (°C)']} °C")
            window["-GPU_MEM-"].update(f"{metrics['GPU Memory (MB)']} MB")
            window["-CPU_MEM-"].update(f"{metrics['CPU Memory (MB)']} MB")
            window["-SD_CARD_CLK-"].update(f"{metrics['SD Card Clock Speed (Hz)']} Hz")
            window["-PIXEL_CLK-"].update(f"{metrics['Pixel Clock Speed (Hz)']} Hz")
            window["-CLOCK_FREQ-"].update(f"{metrics['Clock Frequency (MHz)']} MHz")
            window["-ITER_COUNT-"].update(metrics['Iteration Count'])

