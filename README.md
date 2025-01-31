<!DOCTYPE html>
<html>
<head>
    <title>Event Ticket Generator</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jsbarcode/3.11.5/JsBarcode.all.min.js"></script>
    <style>
        :root {
            --gold: #D4AF37;
            --black: #000000;
        }
        
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 20px;
        }
        
        .container {
            max-width: 800px;
            margin: 0 auto;
        }
        
        .input-form {
            background-color: var(--black);
            color: var(--gold);
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
        }
        
        .input-group {
            margin-bottom: 15px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            color: var(--gold);
        }
        
        input, select {
            width: 100%;
            padding: 8px;
            border: 2px solid var(--gold);
            border-radius: 5px;
            background-color: #1a1a1a;
            color: var(--gold);
        }
        
        .button-group {
            display: flex;
            gap: 10px;
            margin-top: 20px;
            flex-wrap: wrap;
        }
        
        button {
            background-color: var(--gold);
            color: var(--black);
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
            transition: transform 0.2s;
            flex: 1;
        }
        
        button:hover {
            transform: scale(1.05);
        }
        
        .download-options {
            display: none;
            width: 100%;
            margin-top: 10px;
            gap: 10px;
        }
        
        .download-options button {
            background-color: var(--gold);
            opacity: 0.9;
        }
        
        .download-options button:hover {
            opacity: 1;
        }
        
        .ticket {
            width: 21cm;
            height: 29.7cm;
            background-color: white;
            padding: 20px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
            display: none;
            position: relative;
            overflow: hidden;
        }
        
        .watermark {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%) rotate(-45deg);
            font-size: 8em;
            color: rgba(0, 0, 0, 0.05);
            white-space: nowrap;
            pointer-events: none;
            z-index: 1;
            font-weight: bold;
            width: 100%;
            text-align: center;
            user-select: none;
        }
        
        .ticket-header {
            background-color: var(--black);
            color: var(--gold);
            padding: 20px;
            text-align: center;
            border-radius: 10px 10px 0 0;
            border: 2px solid var(--gold);
            position: relative;
        }
        
        .ticket-number-display {
            background-color: var(--gold);
            color: var(--black);
            padding: 10px 20px;
            border-radius: 5px;
            font-weight: bold;
            font-family: monospace;
            font-size: 1.2em;
            position: absolute;
            top: 50%;
            right: 20px;
            transform: translateY(-50%);
            border: 2px solid var(--black);
        }
        
        .ticket-content {
            padding: 40px;
            border: 2px solid var(--black);
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            position: relative;
            z-index: 2;
            background-color: white;
        }
        
        .ticket-info {
            padding: 20px;
        }
        
        .qr-section {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 20px;
            border-left: 2px solid var(--black);
        }
        
        .qr-code {
            margin: 20px 0;
            padding: 15px;
            background: white;
            border: 2px solid var(--black);
            border-radius: 10px;
        }
        
        .qr-code img {
            width: 250px !important;
            height: 250px !important;
        }
        
        .ticket-footer {
            background-color: var(--black);
            color: var(--gold);
            padding: 20px;
            font-size: 0.8em;
            border-radius: 0 0 10px 10px;
            border: 2px solid var(--gold);
            position: relative;
        }
        
        .barcode-container {
            position: absolute;
            bottom: 20px;
            right: 20px;
            text-align: center;
        }
        
        .barcode-container svg {
            background: white;
            padding: 10px;
            border-radius: 5px;
        }
        
        .copyright {
            text-align: center;
            margin-top: 20px;
            padding-top: 15px;
            border-top: 1px solid var(--gold);
            font-size: 0.9em;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="input-form">
            <h2>Event Ticket Generator</h2>
            <div class="input-group">
                <label>Full Name:</label>
                <input type="text" id="fullName" required>
            </div>
            <div class="input-group">
                <label>Email:</label>
                <input type="email" id="email" required>
            </div>
            <div class="input-group">
                <label>Phone Number:</label>
                <input type="tel" id="phone" required>
            </div>
            <div class="input-group">
                <label>Ticket Type:</label>
                <select id="ticketType">
                    <option value="Kids Under 12">Kids Under 12</option>
                    <option value="General">General</option>
                    <option value="VIP">VIP</option>
                </select>
            </div>
            <div class="button-group">
                <button onclick="generateTicket()">Generate Ticket</button>
                <div id="downloadOptions" class="download-options">
                    <button onclick="downloadTicketAsPNG()">Download as PNG</button>
                    <button onclick="downloadTicketAsPDF()">Download as PDF</button>
                </div>
            </div>
        </div>
        
        <div id="ticket" class="ticket">
            <div class="watermark">FLAME AND GRILL</div>
            <div class="ticket-header">
                <h1>FLAME AND GRILL EVENT</h1>
                <div class="ticket-number-display" id="ticketNumber"></div>
            </div>
            <div class="ticket-content">
                <div class="ticket-info">
                    <h3>Event Details</h3>
                    <p><strong>Address:</strong> 1368 Tshepiso, Phase 1, House of Faith, Sharpeville</p>
                    <h3>Ticket Information</h3>
                    <p><strong>Full Name:</strong> <span id="ticketFullName"></span></p>
                    <p><strong>Email:</strong> <span id="ticketEmail"></span></p>
                    <p><strong>Phone:</strong> <span id="ticketPhone"></span></p>
                    <p><strong>Ticket Type:</strong> <span id="ticketTypeDisplay"></span></p>
                    <p><strong>Ticket Price:</strong> <span id="ticketPrice"></span></p>
                    <p><strong>Generated:</strong> <span id="timestamp"></span></p>
                </div>
                <div class="qr-section">
                    <h3>Scan for Verification</h3>
                    <div class="qr-code" id="qrcode"></div>
                    <p>Please present this QR code at the entrance</p>
                </div>
            </div>
            <div class="ticket-footer">
                <h3>Terms and Conditions</h3>
                <p>1. This ticket is a proof of purchase of the above event and must be presented at the time of the admission</p>
                <p>2. This ticket is non-transferable and only one ticket is valid per ticket purchased.</p>
                <p>3. Tickets are not to be duplicated for the purpose of falsifying entry.</p>
                <p>4. Please choose carefully as there are no refunds</p>
                <div class="barcode-container">
                    <svg id="barcode"></svg>
                </div>
                <div class="copyright">
                    <p>© 2025 GraphicCafe. All rights reserved.</p>
                    <p>Developed by Melato Tatu</p>
                </div>
            </div>
        </div>
    </div>

    <script>
        window.jsPDF = window.jspdf.jsPDF;

        function generateTicketNumber() {
            const prefix = 'FGE';
            const timestamp = new Date().getTime().toString().slice(-6);
            const random = Math.random().toString(36).substring(2, 5).toUpperCase();
            return `${prefix}-${timestamp}-${random}`;
        }

        function generateTicket() {
            const fullName = document.getElementById('fullName').value;
            const email = document.getElementById('email').value;
            const phone = document.getElementById('phone').value;
            
            if (!fullName || !email || !phone) {
                alert('Please fill in all fields');
                return;
            }

            const ticketType = document.getElementById('ticketType').value;
            let price;
            switch(ticketType) {
                case 'Kids Under 12':
                    price = 'R150';
                    break;
                case 'General':
                    price = 'R250';
                    break;
                case 'VIP':
                    price = 'R800';
                    break;
            }

            const ticketNumber = generateTicketNumber();
            
            document.getElementById('ticketFullName').textContent = fullName;
            document.getElementById('ticketEmail').textContent = email;
            document.getElementById('ticketPhone').textContent = phone;
            document.getElementById('ticketTypeDisplay').textContent = ticketType;
            document.getElementById('ticketPrice').textContent = price;
            document.getElementById('timestamp').textContent = new Date().toLocaleString();
            document.getElementById('ticketNumber').textContent = ticketNumber;
            document.getElementById('ticket').style.display = 'block';
            document.getElementById('downloadOptions').style.display = 'flex';

            // Generate QR Code
            document.getElementById('qrcode').innerHTML = '';
            const qrData = `
                FLAME AND GRILL EVENT TICKET
                -------------------------
                Ticket #: ${ticketNumber}
                -------------------------
                Full Name: ${fullName}
                Email: ${email}
                Phone: ${phone}
                Ticket Type: ${ticketType}
                Price: ${price}
                -------------------------
                Generated: ${new Date().toLocaleString()}
                Verify at: verify.flameandgrill.com
            `;
            
            new QRCode(document.getElementById('qrcode'), {
                text: qrData,
                width: 250,
                height: 250,
                colorDark: "#000000",
                colorLight: "#ffffff",
                correctLevel: QRCode.CorrectLevel.H
            });

            // Generate Barcode
            JsBarcode("#barcode", ticketNumber, {
                format: "CODE128",
                width: 2,
                height: 50,
                displayValue: true,
                fontSize: 12,
                background: "#ffffff"
            });
        }

        async function downloadTicketAsPNG() {
            const ticket = document.getElementById('ticket');
            try {
                const canvas = await html2canvas(ticket, {
                    scale: 2,
                    useCORS: true,
                    logging: false,
                    backgroundColor: '#ffffff'
                });
                const link = document.createElement('a');
                link.download = 'FlameAndGrill-Ticket.png';
                link.href = canvas.toDataURL('image/png');
                link.click();
            } catch (err) {
                console.error('Error generating ticket image:', err);
                alert('Error generating ticket image. Please try again.');
            }
        }

        async function downloadTicketAsPDF() {
            try {
                const ticket = document.getElementById('ticket');
                const canvas = await html2canvas(ticket, {
                    scale: 2,
                    useCORS: true,
                    logging: false,
                    backgroundColor: '#ffffff'
                });
                const imgData = canvas.toDataURL('image/jpeg', 1.0);

                const pdf = new jsPDF({
                    orientation: 'portrait',
                    unit: 'mm',
                    format: 'a4'
                });

                const imgWidth = 210;
                const imgHeight = (canvas.height * imgWidth) / canvas.width;

                pdf.addImage(imgData, 'JPEG', 0, 0, imgWidth, imgHeight);
                pdf.save('FlameAndGrill-Ticket.pdf');
            } catch (err) {
                console.error('Error generating PDF:', err);
                alert('Error generating PDF. Please try again.');
            }
        }
    </script>
</body>
</html>
