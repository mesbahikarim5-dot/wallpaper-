<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Abdelkarim | 1999 Heart Edition</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #000;
            touch-action: none;
        }

        canvas {
            display: block;
        }
    </style>
</head>
<body>

<canvas id="canvas"></canvas>

<script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d', { willReadFrequently: true });

    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    let particles = [];
    const mouse = { x: null, y: null, radius: 150 };

    const updateMouse = (e) => {
        const x = e.touches ? e.touches[0].clientX : e.clientX;
        const y = e.touches ? e.touches[0].clientY : e.clientY;
        mouse.x = x;
        mouse.y = y;
    };

    window.addEventListener('mousemove', updateMouse);
    window.addEventListener('touchstart', updateMouse);
    window.addEventListener('touchmove', (e) => {
        e.preventDefault();
        updateMouse(e);
    }, { passive: false });

    window.addEventListener('touchend', () => { mouse.x = null; mouse.y = null; });

    class Particle {
        constructor(x, y, color) {
            this.x = Math.random() * canvas.width;
            this.y = Math.random() * canvas.height;
            this.baseX = x;
            this.baseY = y;
            this.size = Math.random() * 2.5 + 1.2;
            this.color = color;
            this.baseColor = color;
            this.density = (Math.random() * 35) + 20;
            this.ease = 0.07;
        }

        draw() {
            ctx.fillStyle = this.color;
            ctx.beginPath();
            ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
            ctx.fill();
        }

        update() {
            let dx = mouse.x - this.x;
            let dy = mouse.y - this.y;
            let distance = Math.sqrt(dx * dx + dy * dy);
            
            if (distance < mouse.radius) {
                let force = (mouse.radius - distance) / mouse.radius;
                let directionX = (dx / distance) * force * this.density;
                let directionY = (dy / distance) * force * this.density;
                this.x -= directionX;
                this.y -= directionY;
                this.color = '#fff'; 
            } else {
                if (this.x !== this.baseX) {
                    this.x -= (this.x - this.baseX) * this.ease;
                }
                if (this.y !== this.baseY) {
                    this.y -= (this.y - this.baseY) * this.ease;
                }
                this.color = this.baseColor;
            }
        }
    }

    function init() {
        particles = [];
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        
        const centerX = canvas.width / 2;
        const H = canvas.height;
        
        // أحجام خطوط أكبر لتغطية المساحة
        let fontSizeName = Math.min(canvas.width / 6, H / 10);
        let fontSizeYear = Math.min(canvas.width / 4, H / 6);
        let heartSize = Math.min(canvas.width / 3, H / 5);

        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';
        ctx.fillStyle = 'white';

        // توزيع العناصر على طول الشاشة
        // 1. الاسم في الثلث العلوي
        ctx.font = `bold ${fontSizeName}px sans-serif`;
        ctx.fillText('ABDELKARIM', centerX, H * 0.15);
        ctx.fillText('MESBAHI', centerX, H * 0.28);

        // 2. القلب في المنتصف تماماً وبحجم ضخم
        ctx.font = `${heartSize}px serif`;
        ctx.fillText('❤', centerX, H * 0.52);

        // 3. سنة 1999 في الثلث السفلي وبحجم بارز
        ctx.font = `900 ${fontSizeYear}px sans-serif`;
        ctx.fillText('1999', centerX, H * 0.82);

        const data = ctx.getImageData(0, 0, canvas.width, canvas.height);
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        const gap = 3; 
        
        // حدود الألوان بناءً على النسب المئوية للارتفاع
        const nameLimit = H * 0.35;
        const heartLimit = H * 0.65;

        for (let y = 0; y < data.height; y += gap) {
            for (let x = 0; x < data.width; x += gap) {
                const alpha = data.data[(y * 4 * data.width) + (x * 4) + 3];
                if (alpha > 128) {
                    let color;
                    if (y < nameLimit) {
                        // الاسم: ذهبي وأصفر
                        color = Math.random() > 0.5 ? '#FFD700' : '#FFFACD';
                    } else if (y < heartLimit) {
                        // القلب: أحمر
                        color = '#FF0000';
                    } else {
                        // 1999: سايان
                        color = '#00FFFF';
                    }
                    particles.push(new Particle(x, y, color));
                }
            }
        }
    }

    function animate() {
        ctx.fillStyle = 'rgba(0, 0, 0, 0.25)';
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        for (let i = 0; i < particles.length; i++) {
            particles[i].update();
            particles[i].draw();
        }
        requestAnimationFrame(animate);
    }

    init();
    animate();

    window.addEventListener('resize', () => {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        init();
    });
</script>

</body>
</html>

