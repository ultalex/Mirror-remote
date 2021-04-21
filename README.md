# Mirror-remote
Application for the MMM-Remote-Control module.
Works only on Wi-Fi networks.
Inaccuracies with the translation into English are possible.
Implemented:
Power and monitor management;
Displaying system information about the raspberry pi; (changes are required in the MMM-Remote-Control module)
VNC launch; (changes are required in the MMM-Remote-Control module)
Widgets for switching profiles, timer, stopwatch, breathing exercises, installed modules (test widget);
The widget displays the module required for installation;
2 languages: Russian (main), English (additional)
Shortcuts;
Working with multiple mirrors;
Custom commands.
![Home](home.jpg)

Changes in the MMM-Remote-Control module
api.js add to line 109 (after  this.configOnHd.modules.filter (mod => skippedModules)

	//системный монитор
		getStats: function() { //http://localhost:6860/api/systemStats?apiKey=vc2ult5476acb
            var fs =  require('fs');
            var os =  require('os');
            //var async = require('async'); //системный монитор (systemStats)
            //var exec = require('child_process').exec; //системный монитор
            var stats = ''

            var temperature = fs.readFileSync("/sys/class/thermal/thermal_zone0/temp");
            temperature = ((temperature/1000).toPrecision(3));
            var cpu = fs.readFileSync("/proc/loadavg");
            cpu = cpu.slice(0,4);
            var upTime = fs.readFileSync("/proc/uptime");
            //var memory = fs.readFileSync(exec("df -h|grep /dev/root|awk '{print $4}'"))
                
            stats = 'temp: '+temperature+'; '+'cpu: '+cpu+'/'+os.cpus().length+'; '+'ram: '+Math.floor(os.freemem()/os.totalmem()*100)+'; '+'uptime: '+os.uptime()+';';//+'memory: '+memory+';';
                
                
            return stats;
		},
        
        //запуск VNC
		startVNC: function() { 
            var exec = require('child_process').exec;
            exec('vncserver-x11 -showstatus');  
            return 'OK';
		},
api.js add to line 156 (after  this.expressRouter.route (['/ test', '/']))

	this.expressRouter.route(['/systemStats']) // //системный монитор без apiKey
            .get((req, res) => {
                if (!this.checkInititialized(res)) { return; }
                res.json({ data: this.getStats() });
            });
api.js add to line 226 (after  this.expressRouter.route ([
'/ refresh /: delayed?',)

	this.expressRouter.route(['/startVNC']) // запуск VNC
            .get((req, res) => {
                if(!this.apiKey && this.secureEndpoints) return res.status(403).json({ success: false, message: "Forbidden: API Key Not Provided in Config! Use secureEndpoints to bypass this message" });
                if (!this.checkInititialized(res)) { return; }
                res.json({ data: this.startVNC()});
            });
