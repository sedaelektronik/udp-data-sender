<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>UDP Data Sender</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 600px;
            margin: 0 auto;
            padding: 20px;
        }
        .container {
            background-color: #f5f5f5;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input[type="text"], textarea {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        textarea {
            height: 100px;
            resize: vertical;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #45a049;
        }
        .button-connect {
            background-color: #2196F3;
            margin-right: 10px;
        }
        .button-connect:hover {
            background-color: #0b7dda;
        }
        .button-group {
            display: flex;
        }
        .result {
            margin-top: 20px;
            padding: 10px;
            border-radius: 4px;
        }
        .success {
            background-color: #dff0d8;
            color: #3c763d;
        }
        .error {
            background-color: #f2dede;
            color: #a94442;
        }
        .info {
            background-color: #d9edf7;
            color: #31708f;
        }
        .checkbox-group {
            margin: 15px 0;
        }
        .checkbox-group label {
            display: inline;
            font-weight: normal;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>UDP Data Sender</h1>
        
        <form method="post">
            <div class="form-group">
                <label for="ip">Destination IP Address:</label>
                <input type="text" id="ip" name="ip" placeholder="Sample: 192.168.1.100" value="<?php echo isset($_POST['ip']) ? htmlspecialchars($_POST['ip']) : ''; ?>" required>
            </div>
            
            <div class="form-group">
                <label for="port">Port Number:</label>
                <input type="text" id="port" name="port" placeholder="Sample: 1234" value="<?php echo isset($_POST['port']) ? htmlspecialchars($_POST['port']) : ''; ?>" required>
            </div>
            
            <div class="form-group">
                <label for="message">Message to be sent:</label>
                <textarea id="message" name="message" placeholder="Type the message or data you want to send..." required><?php echo isset($_POST['message']) ? htmlspecialchars($_POST['message']) : ''; ?></textarea>
            </div>
            
            <div class="checkbox-group">
                <input type="checkbox" id="hex_mode" name="hex_mode" <?php echo isset($_POST['hex_mode']) ? 'checked' : ''; ?>>
                <label for="hex_mode">Send as HEX values (e.g. "01 02 FF A0")</label>
            </div>
            
            <div class="button-group">
                <button type="submit" name="connect" class="button-connect">Connect</button>
                <button type="submit" name="send">Send</button>
            </div>
        </form>
        
        <?php
        if (isset($_POST['connect']) || isset($_POST['send'])) {
            $ip = $_POST['ip'];
            $port = $_POST['port'];
            
            // Validasyon
            if (!filter_var($ip, FILTER_VALIDATE_IP)) {
                echo '<div class="result error">Invalid IP address!</div>';
            } elseif (!is_numeric($port) || $port < 1 || $port > 65535) {
                echo '<div class="result error">Invalid Port Number! (1-65535 arasında olmalı)</div>';
            } else {
                if (isset($_POST['connect'])) {
                    // Sadece bağlantı testi yap
                    $socket = socket_create(AF_INET, SOCK_DGRAM, SOL_UDP);
                    
                    if ($socket === false) {
                        echo '<div class="result error">Failed to create socket: ' . socket_strerror(socket_last_error()) . '</div>';
                    } else {
                        // Soketin oluşturulabildiğini kontrol et (gerçek bağlantı testi için)
                        // UDP bağlantısız protokol olduğundan gerçek bağlantı testi yapılamaz
                        // Sadece soket oluşturma başarısını kontrol edebiliriz
                        echo '<div class="result info">Socket created successfully. Ready to send data to: ' . $ip . ':' . $port . '</div>';
                        socket_close($socket);
                    }
                } elseif (isset($_POST['send'])) {
                    // Mesaj gönderme işlemi
                    $message = $_POST['message'];
                    $hex_mode = isset($_POST['hex_mode']) ? true : false;
                    
                    if (empty($message)) {
                        echo '<div class="result error">Message cannot be empty!</div>';
                    } else {
                        // Hex modunda ise mesajı dönüştür
                        if ($hex_mode) {
                            $hex_values = preg_split('/\s+/', trim($message));
                            $binary_data = '';
                            
                            foreach ($hex_values as $hex) {
                                // Hex değerini temizle (0x veya başındaki boşlukları kaldır)
                                $clean_hex = preg_replace('/[^0-9A-Fa-f]/', '', $hex);
                                
                                if (!empty($clean_hex)) {
                                    // Hex stringini binary'e çevir
                                    $binary_data .= hex2bin(str_pad($clean_hex, 2, '0', STR_PAD_LEFT));
                                }
                            }
                            
                            $message = $binary_data;
                        }
                        
                        // UDP soketi oluştur
                        $socket = socket_create(AF_INET, SOCK_DGRAM, SOL_UDP);
                        
                        if ($socket === false) {
                            echo '<div class="result error">Failed to create socket: ' . socket_strerror(socket_last_error()) . '</div>';
                        } else {
                            // Mesajı gönder
                            $sent = socket_sendto($socket, $message, strlen($message), 0, $ip, $port);
                            
                            if ($sent === false) {
                                echo '<div class="result error">Message could not be sent: ' . socket_strerror(socket_last_error()) . '</div>';
                            } else {
                                $display_message = $hex_mode ? bin2hex($message) : htmlspecialchars($message);
                                echo '<div class="result success">Message sent successfully! Sent to: ' . 
                                     $ip . ':' . $port . '<br>Data: ' . $display_message . 
                                     ' (' . $sent . ' byte)</div>';
                            }
                            
                            // Soketi kapat
                            socket_close($socket);
                        }
                    }
                }
            }
        }
        ?>
    </div>
</body>
</html>
