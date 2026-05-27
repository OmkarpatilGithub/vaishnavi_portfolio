<script>
  import { onMount } from 'svelte';
  import * as THREE from 'three';

  let canvas;
  let activeSection = 'hero';
  let menuOpen = false;
  let tiltCard = { x: 0, y: 0 };

  const skills = [
    { name: 'Java', level: 90, color: '#f89820' },
    { name: 'React JS', level: 88, color: '#61dafb' },
    { name: 'Node.js', level: 82, color: '#68a063' },
    { name: 'JavaScript', level: 85, color: '#f7df1e' },
    { name: 'Docker', level: 75, color: '#2496ed' },
    { name: 'AWS', level: 72, color: '#ff9900' },
    { name: 'MongoDB', level: 78, color: '#47a248' },
    { name: 'MySQL', level: 80, color: '#4479a1' },
    { name: 'Next.js', level: 76, color: '#ffffff' },
    { name: 'CI/CD', level: 74, color: '#64ffda' },
  ];

  const projects = [
    {
      title: 'MEMS – Maharashtra Emergency Medical Services',
      tag: 'Dial 108',
      desc: 'Managed medical case reports, ambulance fleet data, and secure patient data storage. Built high-availability backend with 24/7 support, ticketing system, and load testing for peak traffic.',
      tech: ['Java', 'Node.js', 'MySQL', 'AWS'],
      icon: '🚑',
      color: '#f472b6'
    },
    {
      title: 'UP MVU – Mobile Veterinary Unit',
      tag: 'Field Operations',
      desc: 'Designed optimized relational databases for patient records and treatment histories. Created reports tracking service usage and mobile unit efficiency.',
      tech: ['Java', 'MySQL', 'React JS'],
      icon: '🐾',
      color: '#a78bfa'
    },
    {
      title: 'HHC – Home Healthcare Service',
      tag: 'Healthcare',
      desc: 'Built secure database structures for patient health data and caregiver availability. Performed end-to-end testing ensuring healthcare regulation compliance.',
      tech: ['Java', 'MongoDB', 'Node.js', 'React JS'],
      icon: '🏥',
      color: '#34d399'
    },
    {
      title: 'Good Samaritans Road Safety App',
      tag: 'Safety Tech',
      desc: 'End-to-end testing for real-time GPS tracking, emergency alerts, and high-availability backend services ensuring emergency responders are always reachable.',
      tech: ['Node.js', 'React JS', 'AWS'],
      icon: '🛣️',
      color: '#fbbf24'
    }
  ];

  const experience = [
    {
      role: 'IT Executive',
      company: 'Kinexa Solutions',
      period: 'Dec 2025 – Present',
      color: '#64ffda',
      points: [
        'Managed IT infrastructure, system performance & network security',
        'Supported cloud-based systems and backup/disaster recovery strategies',
        'Monitored servers ensuring high availability of applications',
        'Collaborated on Agile-based project delivery and issue resolution'
      ]
    },
    {
      role: 'Software Engineer',
      company: 'Spero Healthcare Innovations',
      period: 'Nov 2023 – Apr 2025',
      color: '#a78bfa',
      points: [
        'Built scalable healthcare apps with React JS, Java & Node.js',
        'Utilized AWS for hosting, deployment & system scalability',
        'Integrated SonarQube into CI/CD pipelines for automated code quality',
        'Performed backend optimization under high-load conditions'
      ]
    }
  ];

  onMount(() => {
    // Three.js scene
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ canvas, alpha: true, antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

    // Particle field
    const particleCount = 1200;
    const positions = new Float32Array(particleCount * 3);
    const colors = new Float32Array(particleCount * 3);
    const sizes = new Float32Array(particleCount);

    const colorPalette = [
      new THREE.Color('#64ffda'),
      new THREE.Color('#a78bfa'),
      new THREE.Color('#f472b6'),
    ];

    for (let i = 0; i < particleCount; i++) {
      positions[i * 3] = (Math.random() - 0.5) * 40;
      positions[i * 3 + 1] = (Math.random() - 0.5) * 40;
      positions[i * 3 + 2] = (Math.random() - 0.5) * 40;
      const c = colorPalette[Math.floor(Math.random() * 3)];
      colors[i * 3] = c.r;
      colors[i * 3 + 1] = c.g;
      colors[i * 3 + 2] = c.b;
      sizes[i] = Math.random() * 3 + 0.5;
    }

    const geo = new THREE.BufferGeometry();
    geo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
    geo.setAttribute('color', new THREE.BufferAttribute(colors, 3));
    geo.setAttribute('size', new THREE.BufferAttribute(sizes, 1));

    const mat = new THREE.PointsMaterial({
      size: 0.08,
      vertexColors: true,
      transparent: true,
      opacity: 0.7,
      sizeAttenuation: true
    });

    const particles = new THREE.Points(geo, mat);
    scene.add(particles);

    // Floating wireframe torus
    const torusGeo = new THREE.TorusKnotGeometry(3, 0.8, 120, 16);
    const torusMat = new THREE.MeshBasicMaterial({
      color: 0x64ffda,
      wireframe: true,
      transparent: true,
      opacity: 0.12
    });
    const torus = new THREE.Mesh(torusGeo, torusMat);
    torus.position.set(6, 0, -5);
    scene.add(torus);

    // Second torus
    const torus2Geo = new THREE.TorusGeometry(2, 0.4, 20, 60);
    const torus2Mat = new THREE.MeshBasicMaterial({
      color: 0xa78bfa,
      wireframe: true,
      transparent: true,
      opacity: 0.1
    });
    const torus2 = new THREE.Mesh(torus2Geo, torus2Mat);
    torus2.position.set(-6, 2, -8);
    scene.add(torus2);

    camera.position.z = 10;

    let mouseX = 0, mouseY = 0;
    const handleMouse = (e) => {
      mouseX = (e.clientX / window.innerWidth - 0.5) * 2;
      mouseY = (e.clientY / window.innerHeight - 0.5) * 2;
    };
    window.addEventListener('mousemove', handleMouse);

    const handleResize = () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    };
    window.addEventListener('resize', handleResize);

    let animId;
    const clock = new THREE.Clock();
    const animate = () => {
      animId = requestAnimationFrame(animate);
      const t = clock.getElapsedTime();
      particles.rotation.y = t * 0.04 + mouseX * 0.2;
      particles.rotation.x = t * 0.02 + mouseY * 0.1;
      torus.rotation.x = t * 0.3;
      torus.rotation.y = t * 0.2;
      torus2.rotation.x = t * 0.15;
      torus2.rotation.z = t * 0.25;
      camera.position.x += (mouseX * 0.5 - camera.position.x) * 0.03;
      camera.position.y += (-mouseY * 0.5 - camera.position.y) * 0.03;
      renderer.render(scene, camera);
    };
    animate();

    // Intersection Observer for sections
    const sections = document.querySelectorAll('section[id]');
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(e => { if (e.isIntersecting) activeSection = e.target.id; });
    }, { threshold: 0.4 });
    sections.forEach(s => observer.observe(s));

    // Animate skill bars
    const skillObs = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          entry.target.querySelectorAll('.skill-fill').forEach(bar => {
            bar.style.width = bar.dataset.level + '%';
          });
        }
      });
    }, { threshold: 0.2 });
    const skillSection = document.querySelector('#skills');
    if (skillSection) skillObs.observe(skillSection);

    return () => {
      cancelAnimationFrame(animId);
      window.removeEventListener('mousemove', handleMouse);
      window.removeEventListener('resize', handleResize);
      observer.disconnect();
    };
  });

  function handleCardTilt(e, card) {
    const rect = card.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const y = e.clientY - rect.top;
    const cx = rect.width / 2;
    const cy = rect.height / 2;
    card.style.transform = `perspective(800px) rotateX(${((y - cy) / cy) * -8}deg) rotateY(${((x - cx) / cx) * 8}deg) translateZ(10px)`;
  }

  function resetCardTilt(card) {
    card.style.transform = 'perspective(800px) rotateX(0deg) rotateY(0deg) translateZ(0)';
  }
</script>

<canvas bind:this={canvas} id="bg-canvas"></canvas>

<!-- NAV -->
<nav class:open={menuOpen}>
  <div class="nav-logo" on:click={() => document.getElementById('hero').scrollIntoView({behavior:'smooth'})}>
    <span class="logo-bracket">&lt;</span>VU<span class="logo-bracket">/&gt;</span>
  </div>
  <div class="nav-links">
    {#each ['hero','experience','skills','projects','contact'] as s}
      <a href="#{s}" class:active={activeSection === s}
        on:click|preventDefault={() => { document.getElementById(s).scrollIntoView({behavior:'smooth'}); menuOpen=false; }}>
        {s === 'hero' ? 'Home' : s.charAt(0).toUpperCase() + s.slice(1)}
      </a>
    {/each}
  </div>
  <button class="hamburger" on:click={() => menuOpen = !menuOpen} aria-label="Menu">
    <span></span><span></span><span></span>
  </button>
</nav>

<!-- HERO -->
<section id="hero">
  <div class="hero-content">
    <div class="hero-tag">
      <span class="dot"></span> Available for opportunities
    </div>
    <h1 class="hero-name">
      Vaishnavi<br>
      <span class="name-accent">Ugemuge</span>
    </h1>
    <p class="hero-title">Software Engineer <span class="separator">·</span> Full-Stack Developer</p>
    <p class="hero-desc">
      Building scalable healthcare & enterprise applications with Java, React, and cloud-native architectures.
      Based in <span class="highlight">Pune, India</span>.
    </p>
    <div class="hero-actions">
      <a href="#projects" class="btn-primary"
        on:click|preventDefault={() => document.getElementById('projects').scrollIntoView({behavior:'smooth'})}>
        View Projects <span>→</span>
      </a>
      <a href="#contact" class="btn-outline"
        on:click|preventDefault={() => document.getElementById('contact').scrollIntoView({behavior:'smooth'})}>
        Contact Me
      </a>
    </div>
    <div class="hero-stats">
      <div class="stat"><span class="stat-num">1.5+</span><span class="stat-label">Years Exp</span></div>
      <div class="stat-divider"></div>
      <div class="stat"><span class="stat-num">4</span><span class="stat-label">Projects</span></div>
      <div class="stat-divider"></div>
      <div class="stat"><span class="stat-num">10+</span><span class="stat-label">Technologies</span></div>
    </div>
  </div>
  <div class="hero-visual">
    <div class="ring ring-1"></div>
    <div class="ring ring-2"></div>
    <div class="ring ring-3"></div>
    <div class="avatar-hex">
      <div class="avatar-inner">VU</div>
    </div>
    <div class="floating-badge badge-java">☕ Java</div>
    <div class="floating-badge badge-react">⚛ React</div>
    <div class="floating-badge badge-aws">☁ AWS</div>
    <div class="floating-badge badge-docker">🐳 Docker</div>
  </div>
  <div class="scroll-hint">
    <span>scroll</span>
    <div class="scroll-line"></div>
  </div>
</section>

<!-- EXPERIENCE -->
<section id="experience">
  <div class="section-header">
    <span class="section-num"></span>
    <h2>Experience</h2>
    <div class="section-line"></div>
  </div>
  <div class="timeline">
    {#each experience as job, i}
      <div class="timeline-item" style="--color: {job.color}">
        <div class="timeline-dot"></div>
        <div class="timeline-card"
          on:mousemove={(e) => handleCardTilt(e, e.currentTarget)}
          on:mouseleave={(e) => resetCardTilt(e.currentTarget)}>
          <div class="card-glow" style="background: {job.color}"></div>
          <div class="job-header">
            <div>
              <h3 class="job-role">{job.role}</h3>
              <p class="job-company">{job.company}</p>
            </div>
            <span class="job-period">{job.period}</span>
          </div>
          <ul class="job-points">
            {#each job.points as point}
              <li><span class="li-dot" style="color:{job.color}">▸</span> {point}</li>
            {/each}
          </ul>
        </div>
      </div>
    {/each}
  </div>
  <!-- Education -->
  <div class="edu-card"
    on:mousemove={(e) => handleCardTilt(e, e.currentTarget)}
    on:mouseleave={(e) => resetCardTilt(e.currentTarget)}>
    <div class="card-glow" style="background: #fbbf24"></div>
    <span class="edu-icon">🎓</span>
    <div>
      <h3>NBN Sinhgad School of Engineering, Pune</h3>
      <p>B.E. in Electronics & Telecommunication</p>
      <span class="edu-grade">74.16%</span>
    </div>
  </div>
</section>

<!-- SKILLS -->
<section id="skills">
  <div class="section-header">
    <span class="section-num"></span>
    <h2>Skills</h2>
    <div class="section-line"></div>
  </div>
  <div class="skills-grid">
    {#each skills as skill}
      <div class="skill-item">
        <div class="skill-info">
          <span class="skill-name">{skill.name}</span>
          <span class="skill-pct" style="color:{skill.color}">{skill.level}%</span>
        </div>
        <div class="skill-track">
          <div class="skill-fill" data-level={skill.level} style="background: linear-gradient(90deg, {skill.color}, {skill.color}88); width: 0%;"></div>
        </div>
      </div>
    {/each}
  </div>
  <div class="tag-cloud">
    {#each ['CI/CD Pipelines', 'SonarQube', 'Agile/Scrum', 'Git', 'Next.js', 'Django', 'HTML/CSS', 'REST APIs', 'Unit Testing', 'System Design'] as tag}
      <span class="skill-tag">{tag}</span>
    {/each}
  </div>
</section>

<!-- PROJECTS -->
<section id="projects">
  <div class="section-header">
    <span class="section-num"></span>
    <h2>Projects</h2>
    <div class="section-line"></div>
  </div>
  <div class="projects-grid">
    {#each projects as proj}
      <div class="project-card"
        on:mousemove={(e) => handleCardTilt(e, e.currentTarget)}
        on:mouseleave={(e) => resetCardTilt(e.currentTarget)}
        style="--proj-color: {proj.color}">
        <div class="proj-glow" style="background: {proj.color}"></div>
        <div class="proj-top">
          <span class="proj-icon">{proj.icon}</span>
          <span class="proj-tag">{proj.tag}</span>
        </div>
        <h3 class="proj-title">{proj.title}</h3>
        <p class="proj-desc">{proj.desc}</p>
        <div class="proj-tech">
          {#each proj.tech as t}
            <span class="tech-pill" style="border-color: {proj.color}40; color: {proj.color}">{t}</span>
          {/each}
        </div>
      </div>
    {/each}
  </div>
</section>

<!-- ACHIEVEMENTS -->
<section id="achievements" style="padding: 80px 5vw;">
  <div class="section-header">
    <span class="section-num"></span>
    <h2>Achievements</h2>
    <div class="section-line"></div>
  </div>
  <div class="achievements-grid">
    <div class="achieve-card">
      <span class="achieve-icon">🏆</span>
      <h3>State Level Football Champion</h3>
      <p>Winner at state level championship</p>
    </div>
    <div class="achieve-card">
      <span class="achieve-icon">👥</span>
      <h3>NSS Head Coordinator</h3>
      <p>Led 100+ volunteers in National Service</p>
    </div>
    <div class="achieve-card">
      <span class="achieve-icon">💻</span>
      <h3>Full-Stack Java Certified</h3>
      <p>Codenera Pvt. Ltd. — Dec 2022 to May 2023</p>
    </div>
  </div>
</section>

<!-- CONTACT -->
<section id="contact">
  <div class="section-header">
    <span class="section-num"></span>
    <h2>Contact</h2>
    <div class="section-line"></div>
  </div>
  <div class="contact-inner">
    <p class="contact-tagline">Let's build something <span class="highlight">remarkable</span> together.</p>
    <div class="contact-cards">
      <a href="mailto:vaishnaviugemuge7020@gmail.com" class="contact-card">
        <span class="cc-icon">✉</span>
        <span class="cc-label">Email</span>
        <span class="cc-value">vaishnaviugemuge7020@gmail.com</span>
      </a>
      <a href="tel:+917020391340" class="contact-card">
        <span class="cc-icon">📱</span>
        <span class="cc-label">Phone</span>
        <span class="cc-value">+91 7020391340</span>
      </a>
      <a href="https://linkedin.com/in/vaishnavi-ugemuge" target="_blank" class="contact-card">
        <span class="cc-icon">🔗</span>
        <span class="cc-label">LinkedIn</span>
        <span class="cc-value">vaishnavi-ugemuge</span>
      </a>
      <div class="contact-card">
        <span class="cc-icon">📍</span>
        <span class="cc-label">Location</span>
        <span class="cc-value">Pune, Maharashtra</span>
      </div>
    </div>
  </div>
</section>

<footer>
  <p>Designed & Built by <span class="highlight">Vaishnavi Ugemuge</span> · {new Date().getFullYear()}</p>
</footer>

<style>
  #bg-canvas {
    position: fixed;
    top: 0; left: 0;
    width: 100vw; height: 100vh;
    z-index: 0;
    pointer-events: none;
  }

  /* NAV */
  nav {
    position: fixed; top: 0; left: 0; right: 0; z-index: 100;
    display: flex; align-items: center; justify-content: space-between;
    padding: 1.2rem 5vw;
    background: rgba(5,8,16,0.85);
    backdrop-filter: blur(20px);
    border-bottom: 1px solid var(--border);
  }
  .nav-logo {
    font-family: 'Syne', sans-serif;
    font-size: 1.3rem; font-weight: 800;
    color: var(--accent); cursor: pointer; letter-spacing: -0.02em;
  }
  .logo-bracket { color: var(--accent2); }
  .nav-links { display: flex; gap: 2rem; }
  .nav-links a {
    font-size: 0.78rem; text-transform: uppercase; letter-spacing: 0.1em;
    color: var(--muted); text-decoration: none;
    transition: color 0.2s;
    position: relative;
  }
  .nav-links a::after {
    content: ''; position: absolute; bottom: -4px; left: 0; right: 0;
    height: 1px; background: var(--accent);
    transform: scaleX(0); transition: transform 0.2s;
  }
  .nav-links a:hover, .nav-links a.active { color: var(--accent); }
  .nav-links a:hover::after, .nav-links a.active::after { transform: scaleX(1); }
  .hamburger {
    display: none; flex-direction: column; gap: 5px;
    background: none; border: none; cursor: pointer; padding: 4px;
  }
  .hamburger span {
    display: block; width: 22px; height: 2px;
    background: var(--accent); border-radius: 2px; transition: 0.3s;
  }

  /* HERO */
  section#hero {
    min-height: 100vh; padding: 140px 5vw 80px;
    display: flex; align-items: center; gap: 4rem;
    position: relative; z-index: 1;
  }
  .hero-content { flex: 1; max-width: 580px; }
  .hero-tag {
    display: inline-flex; align-items: center; gap: 8px;
    font-size: 0.75rem; letter-spacing: 0.12em; text-transform: uppercase;
    color: var(--accent); background: rgba(100,255,218,0.08);
    border: 1px solid var(--border); border-radius: 20px;
    padding: 6px 16px; margin-bottom: 1.5rem;
  }
  .dot {
    width: 7px; height: 7px; border-radius: 50%;
    background: var(--accent); animation: pulse 2s infinite;
  }
  @keyframes pulse {
    0%,100% { opacity: 1; transform: scale(1); }
    50% { opacity: 0.5; transform: scale(1.4); }
  }
  .hero-name {
    font-family: 'Syne', sans-serif; font-size: clamp(3rem, 7vw, 5.5rem);
    font-weight: 800; line-height: 0.95; letter-spacing: -0.03em;
    color: var(--text); margin-bottom: 1rem;
  }
  .name-accent {
    background: linear-gradient(135deg, var(--accent), var(--accent2));
    -webkit-background-clip: text; -webkit-text-fill-color: transparent;
    background-clip: text;
  }
  .hero-title {
    font-size: 1rem; color: var(--muted); margin-bottom: 1.2rem; letter-spacing: 0.02em;
  }
  .separator { margin: 0 8px; color: var(--accent2); }
  .hero-desc { font-size: 0.93rem; color: #94a3b8; line-height: 1.8; margin-bottom: 2rem; max-width: 460px; }
  .highlight { color: var(--accent); }
  .hero-actions { display: flex; gap: 1rem; margin-bottom: 2.5rem; flex-wrap: wrap; }
  .btn-primary {
    display: inline-flex; align-items: center; gap: 8px;
    padding: 12px 28px; border-radius: 6px; font-size: 0.85rem;
    background: var(--accent); color: var(--bg);
    font-family: 'DM Mono', monospace; font-weight: 400;
    text-decoration: none; transition: all 0.25s; letter-spacing: 0.05em;
  }
  .btn-primary:hover { background: #4adecc; transform: translateY(-2px); box-shadow: 0 8px 25px rgba(100,255,218,0.3); }
  .btn-outline {
    display: inline-flex; align-items: center;
    padding: 12px 28px; border-radius: 6px; font-size: 0.85rem;
    border: 1px solid var(--accent); color: var(--accent);
    font-family: 'DM Mono', monospace; text-decoration: none;
    transition: all 0.25s; letter-spacing: 0.05em;
  }
  .btn-outline:hover { background: rgba(100,255,218,0.08); transform: translateY(-2px); }
  .hero-stats { display: flex; align-items: center; gap: 1.5rem; }
  .stat { display: flex; flex-direction: column; }
  .stat-num { font-family: 'Syne', sans-serif; font-size: 1.8rem; font-weight: 800; color: var(--accent); line-height: 1; }
  .stat-label { font-size: 0.7rem; color: var(--muted); text-transform: uppercase; letter-spacing: 0.1em; margin-top: 2px; }
  .stat-divider { width: 1px; height: 40px; background: var(--border); }

  /* HERO VISUAL */
  .hero-visual {
    flex: 0 0 auto; position: relative; width: 320px; height: 320px;
  }
  .ring {
    position: absolute; border-radius: 50%; border: 1px solid;
    top: 50%; left: 50%; transform: translate(-50%, -50%);
  }
  .ring-1 { width: 200px; height: 200px; border-color: rgba(100,255,218,0.3); animation: spin 15s linear infinite; }
  .ring-2 { width: 270px; height: 270px; border-color: rgba(167,139,250,0.2); animation: spin 25s linear infinite reverse; border-style: dashed; }
  .ring-3 { width: 320px; height: 320px; border-color: rgba(244,114,182,0.15); animation: spin 35s linear infinite; }
  @keyframes spin { to { transform: translate(-50%,-50%) rotate(360deg); } }
  .avatar-hex {
    position: absolute; top: 50%; left: 50%; transform: translate(-50%,-50%);
    width: 120px; height: 120px;
    background: linear-gradient(135deg, rgba(100,255,218,0.15), rgba(167,139,250,0.15));
    border: 1px solid rgba(100,255,218,0.4);
    border-radius: 24px;
    display: flex; align-items: center; justify-content: center;
    backdrop-filter: blur(10px);
  }
  .avatar-inner {
    font-family: 'Syne', sans-serif; font-size: 2.2rem; font-weight: 800;
    background: linear-gradient(135deg, var(--accent), var(--accent2));
    -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;
  }
  .floating-badge {
    position: absolute;
    background: var(--surface2); border: 1px solid var(--border);
    padding: 6px 12px; border-radius: 20px; font-size: 0.72rem;
    color: var(--text); white-space: nowrap;
    backdrop-filter: blur(10px);
  }
  .badge-java { top: 10px; left: 10px; animation: float 4s ease-in-out infinite; }
  .badge-react { top: 10px; right: 10px; animation: float 4s ease-in-out infinite 1s; }
  .badge-aws { bottom: 60px; left: 0; animation: float 4s ease-in-out infinite 0.5s; }
  .badge-docker { bottom: 60px; right: 0; animation: float 4s ease-in-out infinite 1.5s; }
  @keyframes float {
    0%,100% { transform: translateY(0); }
    50% { transform: translateY(-8px); }
  }
  .scroll-hint {
    position: absolute; bottom: 2rem; left: 5vw;
    display: flex; align-items: center; gap: 12px;
    font-size: 0.7rem; letter-spacing: 0.15em; text-transform: uppercase;
    color: var(--muted);
  }
  .scroll-line {
    width: 40px; height: 1px; background: var(--muted);
    animation: scrollLine 2s ease-in-out infinite;
  }
  @keyframes scrollLine { 0%,100%{width:20px} 50%{width:50px} }

  /* SECTIONS SHARED */
  section {
    position: relative; z-index: 1;
    padding: 100px 5vw;
  }
  .section-header {
    display: flex; align-items: center; gap: 1.5rem;
    margin-bottom: 3.5rem;
  }
  .section-num {
    font-family: 'Syne', sans-serif; font-size: 0.72rem;
    color: var(--accent); letter-spacing: 0.15em;
  }
  .section-header h2 {
    font-family: 'Syne', sans-serif; font-size: clamp(1.8rem, 4vw, 2.8rem);
    font-weight: 800; color: var(--text); letter-spacing: -0.02em;
  }
  .section-line { flex: 1; height: 1px; background: var(--border); max-width: 400px; }

  /* TIMELINE */
  .timeline { position: relative; padding-left: 2rem; }
  .timeline::before {
    content: ''; position: absolute; left: 0; top: 8px; bottom: 0;
    width: 1px; background: linear-gradient(to bottom, var(--accent), transparent);
  }
  .timeline-item { position: relative; margin-bottom: 2.5rem; }
  .timeline-dot {
    position: absolute; left: -2.45rem; top: 1.2rem;
    width: 10px; height: 10px; border-radius: 50%;
    background: var(--color); box-shadow: 0 0 12px var(--color);
  }
  .timeline-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 1.5rem 1.8rem;
    position: relative; overflow: hidden;
    transition: transform 0.15s ease, box-shadow 0.3s ease;
    cursor: default;
  }
  .timeline-card:hover { box-shadow: 0 20px 50px rgba(0,0,0,0.4); }
  .card-glow {
    position: absolute; top: -80px; right: -80px;
    width: 200px; height: 200px; border-radius: 50%;
    opacity: 0.06; filter: blur(40px); pointer-events: none;
  }
  .job-header {
    display: flex; justify-content: space-between; align-items: flex-start;
    margin-bottom: 1rem; flex-wrap: wrap; gap: 0.5rem;
  }
  .job-role { font-family: 'Syne', sans-serif; font-size: 1.15rem; font-weight: 700; color: var(--color); }
  .job-company { font-size: 0.83rem; color: var(--muted); margin-top: 2px; }
  .job-period {
    font-size: 0.72rem; background: rgba(100,255,218,0.08);
    border: 1px solid var(--border); padding: 4px 12px; border-radius: 20px;
    color: var(--muted); white-space: nowrap;
  }
  .job-points { list-style: none; display: flex; flex-direction: column; gap: 0.6rem; }
  .job-points li { font-size: 0.85rem; color: #94a3b8; display: flex; gap: 8px; align-items: flex-start; }
  .li-dot { flex-shrink: 0; }

  /* EDUCATION */
  .edu-card {
    background: var(--surface); border: 1px solid rgba(251,191,36,0.2);
    border-radius: 12px; padding: 1.5rem 1.8rem;
    display: flex; align-items: center; gap: 1.5rem;
    position: relative; overflow: hidden;
    transition: transform 0.15s ease; cursor: default; margin-top: 1rem;
  }
  .edu-icon { font-size: 2rem; }
  .edu-card h3 { font-family: 'Syne', sans-serif; font-size: 1.05rem; font-weight: 700; color: #fbbf24; }
  .edu-card p { font-size: 0.83rem; color: var(--muted); margin-top: 3px; }
  .edu-grade {
    display: inline-block; margin-top: 8px;
    font-size: 0.75rem; background: rgba(251,191,36,0.12);
    color: #fbbf24; padding: 3px 10px; border-radius: 4px; border: 1px solid rgba(251,191,36,0.3);
  }

  /* SKILLS */
  .skills-grid {
    display: grid; grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 1.2rem; margin-bottom: 2.5rem;
  }
  .skill-item {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 10px; padding: 1rem 1.2rem;
  }
  .skill-info { display: flex; justify-content: space-between; margin-bottom: 8px; }
  .skill-name { font-size: 0.83rem; color: var(--text); }
  .skill-pct { font-size: 0.78rem; font-weight: 400; }
  .skill-track {
    height: 4px; background: var(--surface2); border-radius: 2px; overflow: hidden;
  }
  .skill-fill {
    height: 100%; border-radius: 2px; width: 0%;
    transition: width 1.2s cubic-bezier(0.25, 1, 0.5, 1);
  }
  .tag-cloud { display: flex; flex-wrap: wrap; gap: 0.6rem; }
  .skill-tag {
    font-size: 0.75rem; padding: 6px 14px;
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 20px; color: var(--muted);
    transition: all 0.2s;
  }
  .skill-tag:hover { border-color: var(--accent); color: var(--accent); background: rgba(100,255,218,0.06); }

  /* PROJECTS */
  .projects-grid {
    display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 1.5rem;
  }
  .project-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 14px; padding: 1.8rem;
    position: relative; overflow: hidden;
    transition: transform 0.15s ease, box-shadow 0.3s ease;
    cursor: default;
  }
  .project-card:hover {
    border-color: var(--proj-color);
    box-shadow: 0 20px 50px rgba(0,0,0,0.4), 0 0 30px color-mix(in srgb, var(--proj-color) 15%, transparent);
  }
  .proj-glow {
    position: absolute; top: -60px; right: -60px;
    width: 180px; height: 180px; border-radius: 50%;
    opacity: 0.07; filter: blur(30px); pointer-events: none;
    transition: opacity 0.3s;
  }
  .project-card:hover .proj-glow { opacity: 0.15; }
  .proj-top { display: flex; justify-content: space-between; align-items: center; margin-bottom: 1rem; }
  .proj-icon { font-size: 1.8rem; }
  .proj-tag {
    font-size: 0.7rem; padding: 4px 10px; border-radius: 4px;
    background: color-mix(in srgb, var(--proj-color) 12%, transparent);
    color: var(--proj-color);
    border: 1px solid color-mix(in srgb, var(--proj-color) 30%, transparent);
    letter-spacing: 0.08em;
  }
  .proj-title {
    font-family: 'Syne', sans-serif; font-size: 1rem; font-weight: 700;
    color: var(--text); margin-bottom: 0.7rem; line-height: 1.3;
  }
  .proj-desc { font-size: 0.82rem; color: #94a3b8; line-height: 1.7; margin-bottom: 1.2rem; }
  .proj-tech { display: flex; flex-wrap: wrap; gap: 0.4rem; }
  .tech-pill {
    font-size: 0.7rem; padding: 3px 10px; border-radius: 4px; border: 1px solid;
  }

  /* ACHIEVEMENTS */
  .achievements-grid {
    display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
    gap: 1.2rem;
  }
  .achieve-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 1.5rem;
    text-align: center; transition: all 0.25s;
  }
  .achieve-card:hover { border-color: var(--accent2); transform: translateY(-4px); }
  .achieve-icon { font-size: 2.2rem; display: block; margin-bottom: 0.8rem; }
  .achieve-card h3 { font-family: 'Syne', sans-serif; font-size: 0.95rem; color: var(--text); margin-bottom: 0.4rem; }
  .achieve-card p { font-size: 0.8rem; color: var(--muted); }

  /* CONTACT */
  section#contact { text-align: center; }
  .contact-inner { max-width: 700px; margin: 0 auto; }
  .contact-tagline {
    font-family: 'Syne', sans-serif; font-size: clamp(1.5rem, 3.5vw, 2.2rem);
    font-weight: 700; margin-bottom: 2.5rem; color: var(--text);
  }
  .contact-cards { display: grid; grid-template-columns: repeat(2, 1fr); gap: 1rem; }
  .contact-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 1.3rem 1.5rem;
    display: flex; flex-direction: column; gap: 4px;
    text-decoration: none; transition: all 0.2s;
  }
  .contact-card:hover { border-color: var(--accent); transform: translateY(-3px); }
  .cc-icon { font-size: 1.4rem; }
  .cc-label { font-size: 0.7rem; color: var(--muted); text-transform: uppercase; letter-spacing: 0.1em; }
  .cc-value { font-size: 0.82rem; color: var(--text); word-break: break-all; }

  /* FOOTER */
  footer {
    text-align: center; padding: 2rem;
    border-top: 1px solid var(--border);
    font-size: 0.78rem; color: var(--muted);
    position: relative; z-index: 1;
  }

  /* RESPONSIVE */
  @media (max-width: 768px) {
    section#hero { flex-direction: column; gap: 3rem; padding-top: 120px; }
    .hero-visual { width: 250px; height: 250px; }
    .nav-links { display: none; }
    .hamburger { display: flex; }
    nav.open .nav-links {
      display: flex; flex-direction: column;
      position: absolute; top: 100%; left: 0; right: 0;
      background: rgba(5,8,16,0.97); padding: 1.5rem;
      border-bottom: 1px solid var(--border); gap: 1.2rem;
    }
    .contact-cards { grid-template-columns: 1fr; }
    .hero-visual { display: none; }
  }
</style>
