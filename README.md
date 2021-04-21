# Mirror-remote
Application for the MMM-Remote-Control module
 
Работает только в сетях Wi-Fi.
Возможны неточности в переводе на английский, посоветуйте, пожалуйста, как исправить перевод. Я также буду рад получить идеи для новых функций или виджетов и переводов.
Реализовано:
Управление питанием и монитором;
Отображение системной информации о Raspberry Pi; (необходимы изменения в модуле MMM-Remote-Control)
Запуск VNC; (требуются изменения в модуле MMM-Remote-Control)
Виджеты переключения профилей, таймер, секундомер, дыхательные упражнения, установленные модули (тестовый виджет);
Виджет отображает необходимый для установки модуль;
2 языка: русский (основной), английский (дополнительный)
Горячие клавиши;
Работа с несколькими зеркалами;
Пользовательские команды. Приложение
Дом
Модули
Настройки
Добавить зеркало
Добавить команду
Ярлыки

Изменения в модуле MMM-Remote-Control
api.js добавить в строку 109 (после this.configOnHd.modules.filter (mod => skippedModules)

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
api.js добавить в строку 156 (после this.expressRouter.route (['/ test', '/']))

this.expressRouter.route(['/systemStats']) // //системный монитор без apiKey
            .get((req, res) => {
                if (!this.checkInititialized(res)) { return; }
                res.json({ data: this.getStats() });
            });
api.js добавить в строку 226 (после this.expressRouter.route ([
'/ refresh /: delayed?',)

this.expressRouter.route(['/startVNC']) // запуск VNC
            .get((req, res) => {
                if(!this.apiKey && this.secureEndpoints) return res.status(403).json({ success: false, message: "Forbidden: API Key Not Provided in Config! Use secureEndpoints to bypass this message" });
                if (!this.checkInititialized(res)) { return; }
                res.json({ data: this.startVNC()});
            });
