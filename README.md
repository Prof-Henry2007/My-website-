<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>St. Louis SHS Portal -- Demo</title>
<style>
  :root{
    --brand:#1e6e4c;
    --dark:#0f1113;
    --panel:#16181b;
    --muted:#9ea3a8;
    --card:#222429;
  }
  *{box-sizing:border-box}
  body{
    margin:0;
    font-family:Inter, "Segoe UI", Roboto, system-ui, -apple-system, sans-serif;
    background:#f5f7fa;
    color:#111;
    -webkit-font-smoothing:antialiased;
    -moz-osx-font-smoothing:grayscale;
  }
  a{color:inherit;text-decoration:none}
  /* ---------- Header (fixed) ---------- */
  header{
    position:fixed; top:0; left:0; right:0; z-index:1400;
    display:flex; align-items:center; justify-content:space-between;
    padding:10px 18px;
    background:linear-gradient(180deg,#121317 0,#17181b 100%);
    color:#fff;
    box-shadow:0 6px 22px rgba(0,0,0,0.28);
  }
  .header-left{display:flex;align-items:center;gap:12px}
  .logo-wrap{display:flex;align-items:center;gap:10px}
  .logo{
    width:44px;height:44px;border-radius:8px;overflow:hidden;flex:0 0 44px;background:#fff;
    display:inline-block;
  }
  .logo img{width:100%;height:100%;object-fit:contain;display:block}
  .title{font-weight:700;font-size:1.05rem;letter-spacing:0.25px}
  /* ---------- top actions ---------- */
  .top-actions{display:flex;align-items:center;gap:10px}
  .nav-button{
    background:transparent; color:#fff;
    border:1px solid rgba(255,255,255,0.06);
    padding:8px 12px; border-radius:8px;
    cursor:pointer; font-weight:600;
  }
  .nav-button:hover{background:rgba(255,255,255,0.03)}
  .news-btn{background:linear-gradient(90deg,var(--brand),#4fb97f); color:#fff; box-shadow:0 8px 26px rgba(30,110,76,0.12)}
  /* hamburger */
  .hamburger{
    width:40px; height:32px; display:flex; flex-direction:column; justify-content:space-between;
    cursor:pointer; padding:6px; border-radius:8px; background:transparent;
  }
  .hamburger span{display:block;height:3px;border-radius:3px;background:#fff;transition:all .25s}
  .hamburger.active span:nth-child(1){transform:rotate(45deg) translateY(6px)}
  .hamburger.active span:nth-child(2){opacity:0}
  .hamburger.active span:nth-child(3){transform:rotate(-45deg) translateY(-6px)}

  /* ---------- Main dropdown menu (hamburger) ---------- */
  .main-menu{
    position:fixed; right:18px; top:64px; width:280px;
    background:#fff; color:#111; border-radius:12px;
    box-shadow:0 22px 50px rgba(6,6,6,0.3); overflow:hidden;
    transform-origin:top right;
    opacity:0; pointer-events:none; transform:translateY(-10px) scale(.98);
    transition:all .22s ease;
  }
  .main-menu.active{opacity:1; transform:translateY(0) scale(1); pointer-events:auto}
  .main-menu ul{list-style:none; margin:0; padding:8px 0}
  .main-menu li{border-bottom:1px solid #f1f1f3}
  .main-menu li:last-child{border-bottom:0}
  .main-menu a{display:block;padding:12px 16px;color:#222;font-weight:600}
  .main-menu a:hover{background:#f2f6f8}
  /* submenu inside */
  .submenu{display:none;background:#e8fef0}
  .submenu.active{display:block}
  .submenu a{padding-left:30px; font-weight:600; color:#0e3b26}
  /* colored submenus: academics = blue, clubs = orange */
  #academicsSubmenu{background:#e8f4ff}
  #clubsSubmenu{background:#fff4e6}

  /* ---------- News panel (slide down, dark) ---------- */
  .news-panel{
    position:fixed; left:0; right:0; top:0; z-index:1300;
    background:var(--dark); color:#e7f0f0;
    transform:translateY(-120%); transition:transform .40s cubic-bezier(.2,.9,.3,1);
    box-shadow:0 22px 60px rgba(0,0,0,0.5);
    border-bottom:3px solid rgba(255,255,255,0.03);
  }
  .news-panel.open{transform:translateY(0)}
  .news-inner{
    max-width:1100px;margin:0 auto;padding:18px;display:flex;gap:20px;
    align-items:flex-start;
  }
  .news-left{flex:1 1 520px}
  .news-right{width:340px;flex:0 0 340px}
  .panel-header{display:flex;align-items:center;justify-content:space-between;gap:12px;margin-bottom:12px}
  .panel-header h3{margin:0;color:#fff;font-size:1.05rem}
  .panel-controls{display:flex;gap:8px;align-items:center}
  .close-news{background:transparent;border:1px solid rgba(255,255,255,0.06);padding:8px;border-radius:8px;color:#fff;cursor:pointer}

  /* news viewport */
  .news-viewport{height:360px; overflow:hidden; position:relative; border-radius:10px; border:1px solid rgba(255,255,255,0.03)}
  .news-track{
    display:flex;flex-direction:column;
    animation:scrollNews 36s linear infinite; /* slow & smooth: long duration */
  }
  .news-track.paused{animation-play-state:paused}
  .news-card{
    display:flex;gap:14px;padding:14px;background:linear-gradient(180deg,rgba(255,255,255,0.02),transparent);
    border-bottom:1px solid rgba(255,255,255,0.02);
    align-items:flex-start;
  }
  .news-thumb{width:120px;height:78px;border-radius:8px;flex:0 0 120px;background-size:cover;background-position:center;box-shadow:0 6px 18px rgba(0,0,0,0.5)}
  .news-body{flex:1}
  .news-title{font-weight:700;color:#fff;margin-bottom:6px;font-size:15px}
  .news-meta{font-size:12px;color:var(--muted);margin-bottom:8px}
  .news-snippet{font-size:14px;color:#d9efee;margin-bottom:10px}
  .news-actions{display:flex;gap:8px}
  .news-actions a{background:transparent;border:1px solid rgba(255,255,255,0.08);padding:7px 10px;border-radius:8px;color:#fff;font-size:13px}
  .news-actions a:hover{background:rgba(255,255,255,0.03)}

  /* Right column (highlights) */
  .news-highlights{background:transparent;padding:10px;border-radius:8px}
  .highlight{background:var(--panel);padding:12px;border-radius:8px;margin-bottom:12px;color:#fff}
  .highlight h4{margin:0 0 6px 0;font-size:14px}
  .highlight p{margin:0;color:var(--muted);font-size:13px}

  .hover-hint{font-size:12px;color:var(--muted);margin-left:8px}

  /* ---------- Auto-scroll keyframes ---------- */
  @keyframes scrollNews{
    0%{transform:translateY(0)}
    100%{transform:translateY(-50%)} /* duplicate feed so -50% loops seamlessly */
  }

  /* ---------- Main content (below fixed header) ---------- */
  main{max-width:1000px;margin:120px auto 60px; padding:0 16px; transition:0.3s}
  .welcome{background:#fff;padding:26px;border-radius:12px;box-shadow:0 8px 30px rgba(0,0,0,0.06);text-align:center}
  .hidden-section{display:none;background:#fff;padding:20px;border-radius:12px;margin-top:18px;box-shadow:0 8px 30px rgba(0,0,0,0.06)}
  .hidden-section.active{display:block}
  .hidden-section h2{color:var(--brand);margin-bottom:8px}
  .hidden-section p{color:#333;line-height:1.5}
  .hidden-section .images{display:flex;gap:12px;margin-top:12px;flex-wrap:wrap}
  .hidden-section img{width:100%;max-width:360px;border-radius:10px;box-shadow:0 6px 18px rgba(0,0,0,0.06)}

  /* ---------- Responsive ---------- */
  @media (max-width:980px){
    .news-inner{flex-direction:column}
    .news-right{width:100%}
    .news-viewport{height:300px}
  }
  @media (max-width:700px){
    .main-menu{right:10px; width:94vw}
    .news-viewport{height:220px}
    .logo{width:40px;height:40px}
    .title{font-size:0.96rem}
  }
</style>
</head>
<body>

<header>
  <div class="header-left">
    <div class="logo-wrap">
      <div class="logo" aria-hidden="true">
        <!-- Logo sourced online (temporary) -->
        <img src="https://pbs.twimg.com/profile_images/878501293219827712/6yyLjZMF_400x400.jpg" alt="St. Louis SHS logo">
      </div>
      <div class="title">St. Louis SHS Portal</div>
    </div>
  </div>

  <div class="top-actions">
    <button class="nav-button nav-button--home" id="homeBtn">Home</button>
    <button class="nav-button news-btn" id="newsBtn">News &amp; Events</button>

    <div class="hamburger" id="hamburgerBtn" aria-label="Open menu" title="Menu">
      <span></span><span></span><span></span>
    </div>
  </div>
</header>

<!-- Main hamburger dropdown -->
<nav class="main-menu" id="mainMenu" aria-hidden="true">
  <ul>
    <li><a href="#" id="homeBtnMenu">Home</a></li>

    <li>
      <a href="#" id="aboutToggle">About Us ▾</a>
      <ul class="submenu" id="aboutSubmenu">
        <li><a href="#" id="historyBtn">History</a></li>
        <li><a href="#" id="missionBtn">Mission &amp; Vision</a></li>
        <li><a href="#" id="adminBtn">Administration</a></li>
        <li><a href="#" id="achieveBtn">Achievements</a></li>
      </ul>
    </li>

    <li>
      <a href="#" id="academicsToggle">Academics ▾</a>
      <ul class="submenu" id="academicsSubmenu">
        <li><a href="#" id="programsBtn">Programmes</a></li>
        <li><a href="#" id="departmentsBtn">Departments</a></li>
        <li><a href="#" id="coursesBtn">Courses</a></li>
        <li><a href="#" id="libraryBtn">Library</a></li>
      </ul>
    </li>

    <li>
      <a href="#" id="clubsToggle">Clubs | Societies ▾</a>
      <ul class="submenu" id="clubsSubmenu">
        <li><a href="#" id="gunsaBtn">GUNSA</a></li>
        <li><a href="#" id="businessClubBtn">Business Club</a></li>
        <li><a href="#" id="dramaClubBtn">Drama &amp; Debate</a></li>
        <li><a href="#" id="stemClubBtn">STEM Club</a></li>
      </ul>
    </li>

    <li><a href="#" id="admissionsBtn">Admissions</a></li>
    <li><a href="#" id="contactBtn">Contact</a></li>
  </ul>
</nav>

<!-- News Panel (dark) -->
<div class="news-panel" id="newsPanel" aria-hidden="true">
  <div class="news-inner">
    <div class="news-left">
      <div class="panel-header">
        <h3>News &amp; Events -- Ghana SHS Feed (offline samples)</h3>
        <div class="panel-controls">
          <span class="hover-hint">Hover news to pause</span>
          <button class="close-news" id="closeNewsBtn">Close</button>
        </div>
      </div>

      <!-- viewport with auto-scrolling track -->
      <div class="news-viewport" id="newsViewport" title="News feed -- hover to pause">
        <div class="news-track" id="newsTrack">
          <!-- First copy -->
          <div class="news-card">
            <div class="news-thumb" style="background-image:url('https://upload.wikimedia.org/wikipedia/commons/3/3f/St._Louis_Senior_High_School.jpg')"></div>
            <div class="news-body">
              <div class="news-title">St. Louis SHS marks milestone anniversary</div>
              <div class="news-meta">Oct 10, 2024 -- School News</div>
              <div class="news-snippet">St. Louis SHS celebrated its 70th anniversary with alumni, upgrades to science labs and community events.</div>
              <div class="news-actions"><a href="https://en.wikipedia.org/wiki/St._Louis_Senior_High_School_(Ghana)" target="_blank" rel="noopener">Read more</a></div>
            </div>
          </div>

          <div class="news-card">
            <div class="news-thumb" style="background-image:url('https://upload.wikimedia.org/wikipedia/commons/d/d7/Students_in_Ghana.jpg')"></div>
            <div class="news-body">
              <div class="news-title">NSMQ 2025: regional qualifiers update</div>
              <div class="news-meta">Feb 18, 2025 -- NSMQ</div>
              <div class="news-snippet">Regional heats continue -- top performing schools prepare for nationals. Stay updated for live streams and fixtures.</div>
              <div class="news-actions"><a href="https://www.nsmq.com.gh/" target="_blank" rel="noopener">Read more</a></div>
            </div>
          </div>

          <div class="news-card">
            <div class="news-thumb" style="background-image:url('https://upload.wikimedia.org/wikipedia/commons/7/7e/Library_Ghana.jpg')"></div>
            <div class="news-body">
              <div class="news-title">GES initiative: improving SHS infrastructure</div>
              <div class="news-meta">Mar 05, 2025 -- GES</div>
              <div class="news-snippet">Ghana Education Service announces targeted funding for classroom blocks and staff accommodation at several SHSs.</div>
              <div class="news-actions"><a href="https://ges.gov.gh/" target="_blank" rel="noopener">Read more</a></div>
            </div>
          </div>

          <div class="news-card">
            <div class="news-thumb" style="background-image:url('https://upload.wikimedia.org/wikipedia/commons/0/0d/Robotics_team_Ghana.jpg')"></div>
            <div class="news-body">
              <div class="news-title">STEM club wins robotics accolade</div>
              <div class="news-meta">Apr 14, 2025 -- STEM News</div>
              <div class="news-snippet">The school's STEM Club represented Ghana at an international competition and returned with recognition for innovation.</div>
              <div class="news-actions"><a href="#" target="_blank" rel="noopener">Read more</a></div>
            </div>
          </div>

          <!-- Duplicate the same set to make seamless loop (CSS translates -50%) -->
          <div class="news-card">
            <div class="news-thumb" style="background-image:url('https://upload.wikimedia.org/wikipedia/commons/3/3f/St._Louis_Senior_High_School.jpg')"></div>
            <div class="news-body">
              <div class="news-title">St. Louis SHS marks milestone anniversary</div>
              <div class="news-meta">Oct 10, 2024 -- School News</div>
              <div class="news-snippet">St. Louis SHS celebrated its 70th anniversary with alumni, upgrades to science labs and community events.</div>
              <div class="news-actions"><a href="https://en.wikipedia.org/wiki/St._Louis_Senior_High_School_(Ghana)" target="_blank" rel="noopener">Read more</a></div>
            </div>
          </div>

          <div class="news-card">
            <div class="news-thumb" style="background-image:url('https://upload.wikimedia.org/wikipedia/commons/d/d7/Students_in_Ghana.jpg')"></div>
            <div class="news-body">
              <div class="news-title">NSMQ 2025: regional qualifiers update</div>
              <div class="news-meta">Feb 18, 2025 -- NSMQ</div>
              <div class="news-snippet">Regional heats continue -- top performing schools prepare for nationals. Stay updated for live streams and fixtures.</div>
              <div class="news-actions"><a href="https://www.nsmq.com.gh/" target="_blank" rel="noopener">Read more</a></div>
            </div>
          </div>

          <div class="news-card">
            <div class="news-thumb" style="background-image:url('https://upload.wikimedia.org/wikipedia/commons/7/7e/Library_Ghana.jpg')"></div>
            <div class="news-body">
              <div class="news-title">GES initiative: improving SHS infrastructure</div>
              <div class="news-meta">Mar 05, 2025 -- GES</div>
              <div class="news-snippet">Ghana Education Service announces targeted funding for classroom blocks and staff accommodation at several SHSs.</div>
              <div class="news-actions"><a href="https://ges.gov.gh/" target="_blank" rel="noopener">Read more</a></div>
            </div>
          </div>

          <div class="news-card">
            <div class="news-thumb" style="background-image:url('https://upload.wikimedia.org/wikipedia/commons/0/0d/Robotics_team_Ghana.jpg')"></div>
            <div class="news-body">
              <div class="news-title">STEM club wins robotics accolade</div>
              <div class="news-meta">Apr 14, 2025 -- STEM News</div>
              <div class="news-snippet">The school's STEM Club represented Ghana at an international competition and returned with recognition for innovation.</div>
              <div class="news-actions"><a href="#" target="_blank" rel="noopener">Read more</a></div>
            </div>
          </div>

        </div><!-- end news-track -->
      </div><!-- end viewport -->
    </div><!-- left -->

    <aside class="news-right">
      <div class="panel-header" style="margin-bottom:10px">
        <h3 style="font-size:0.95rem">Highlights</h3>
      </div>

      <div class="news-highlights">
        <div class="highlight">
          <h4>Upcoming: NSMQ Nationals</h4>
          <p>Follow live streams and scheduled fixtures for NSMQ 2025 -- national stage.</p>
        </div>
        <div class="highlight">
          <h4>GES Funding</h4>
          <p>Infrastructure projects announced for selected SHSs across regions.</p>
        </div>
        <div class="highlight">
          <h4>Alumni Reunion</h4>
          <p>St. Louis alumni weekend: mentorship & fundraising events.</p>
        </div>
      </div>
    </aside>
  </div><!-- news-inner -->
</div><!-- news-panel -->

<!-- Main content -->
<main id="contentArea">
  <section class="welcome" id="welcomeSection">
    <h1>Welcome to St. Louis Senior High School (Demo)</h1>
    <p>Empowering young women through faith, discipline and academic excellence.</p>
  </section>

  <!-- sample info sections (opened by menu items) -->
  <section class="hidden-section" id="historySection">
    <h2>History</h2>
    <p>St. Louis Senior High School, Kumasi, founded by the Sisters of St. Louis, has grown into one of Ghana's well-known girls' schools.</p>
    <div class="images"><img src="https://upload.wikimedia.org/wikipedia/commons/3/3f/St._Louis_Senior_High_School.jpg" alt="campus"></div>
  </section>

  <section class="hidden-section" id="missionSection">
    <h2>Mission &amp; Vision</h2>
    <p>To provide quality education and holistic formation for girls to become responsible women of integrity and excellence.</p>
    <div class="images"><img src="https://upload.wikimedia.org/wikipedia/commons/5/55/Graduation_ceremony_in_Ghana.jpg" alt=""></div>
  </section>

  <section class="hidden-section" id="programsSection">
    <h2>Programmes</h2>
    <p>Programmes: General Science, General Arts, Business, Home Economics, Visual Arts.</p>
    <div class="images"><img src="https://upload.wikimedia.org/wikipedia/commons/8/84/Physics_lab_Ghana.jpg" alt=""></div>
  </section>

  <section class="hidden-section" id="gunsaSection">
    <h2>GUNSA</h2>
    <p>GUNSA promotes Model UN activities and global awareness among students.</p>
    <div class="images"><img src="https://upload.wikimedia.org/wikipedia/commons/a/a0/Model_UN_Ghana.jpg" alt=""></div>
  </section>

  <section class="hidden-section" id="stemClubSection">
    <h2>STEM Club</h2>
    <p>The STEM Club pushes robotics, coding and competitions -- champions of innovation.</p>
    <div class="images"><img src="https://upload.wikimedia.org/wikipedia/commons/0/0d/Robotics_team_Ghana.jpg" alt=""></div>
  </section>
</main>

<script>
  /* ---------- Elements ---------- */
  const newsBtn = document.getElementById('newsBtn');
  const newsPanel = document.getElementById('newsPanel');
  const closeNewsBtn = document.getElementById('closeNewsBtn');
  const newsTrack = document.getElementById('newsTrack');
  const newsViewport = document.getElementById('newsViewport');

  const hamburgerBtn = document.getElementById('hamburgerBtn');
  const mainMenu = document.getElementById('mainMenu');

  const homeBtn = document.getElementById('homeBtn');
  const homeBtnMenu = document.getElementById('homeBtnMenu');

  // submenu toggles
  const aboutToggle = document.getElementById('aboutToggle');
  const aboutSubmenu = document.getElementById('aboutSubmenu');
  const academicsToggle = document.getElementById('academicsToggle');
  const academicsSubmenu = document.getElementById('academicsSubmenu');
  const clubsToggle = document.getElementById('clubsToggle');
  const clubsSubmenu = document.getElementById('clubsSubmenu');

  // info sections map (element IDs follow pattern)
  const sections = {
    historyBtn: document.getElementById('historySection'),
    missionBtn: document.getElementById('missionSection'),
    programsBtn: document.getElementById('programsSection'),
    gunsaBtn: document.getElementById('gunsaSection'),
    stemClubBtn: document.getElementById('stemClubSection')
  };

  /* ---------- News panel controls ---------- */
  function openNews(){
    // close menu if open
    mainMenu.classList.remove('active');
    hamburgerBtn.classList.remove('active');

    newsPanel.classList.toggle('open');
    newsPanel.setAttribute('aria-hidden', newsPanel.classList.contains('open') ? 'false' : 'true');
  }
  newsBtn.addEventListener('click', openNews);
  closeNewsBtn.addEventListener('click', ()=>{ newsPanel.classList.remove('open'); newsPanel.setAttribute('aria-hidden','true') });

  // pause news auto-scroll on hover (toggle class)
  newsViewport.addEventListener('mouseenter', ()=> newsTrack.classList.add('paused'));
  newsViewport.addEventListener('mouseleave', ()=> newsTrack.classList.remove('paused'));

  /* ---------- Hamburger menu ---------- */
  hamburgerBtn.addEventListener('click', () => {
    hamburgerBtn.classList.toggle('active');
    mainMenu.classList.toggle('active');
  });

  /* ---------- Submenu toggles (mobile tap) ---------- */
  function closeAllSubmenus(){
    [aboutSubmenu,academicsSubmenu,clubsSubmenu].forEach(sm => sm.classList.remove('active'));
  }
  aboutToggle && aboutToggle.addEventListener('click', e => { e.preventDefault(); closeAllSubmenus(); aboutSubmenu.classList.toggle('active'); });
  academicsToggle && academicsToggle.addEventListener('click', e => { e.preventDefault(); closeAllSubmenus(); academicsSubmenu.classList.toggle('active'); });
  clubsToggle && clubsToggle.addEventListener('click', e => { e.preventDefault(); closeAllSubmenus(); clubsSubmenu.classList.toggle('active'); });

  /* ---------- Section display logic ---------- */
  function hideAllSections(){
    document.querySelectorAll('.hidden-section').forEach(s => s.classList.remove('active'));
    document.getElementById('welcomeSection').classList.remove('active'); // if previously set
  }
  // map menu button ids to section ids:
  const btnToSection = {
    historyBtn: 'historySection',
    missionBtn: 'missionSection',
    programsBtn: 'programsSection',
    gunsaBtn: 'gunsaSection',
    stemClubBtn: 'stemClubSection'
  };

  // attach click listeners for each button (if present)
  Object.keys(btnToSection).forEach(btnId => {
    const btn = document.getElementById(btnId);
    const secId = btnToSection[btnId];
    if(!btn) return;
    btn.addEventListener('click', e=>{
      e.preventDefault();
      hideAllSections();
      document.getElementById(secId).classList.add('active');
      // close menus & news panel
      mainMenu.classList.remove('active'); hamburgerBtn.classList.remove('active');
      newsPanel.classList.remove('open');
      // scroll to content just under header
      const y = document.getElementById(secId).getBoundingClientRect().top + window.scrollY - 80;
      window.scrollTo({behavior:'smooth', top: y});
    });
  });

  // Home button clears everything
  [homeBtn, homeBtnMenu].forEach(hb => {
    hb.addEventListener('click', e=>{
      e.preventDefault();
      hideAllSections();
      // show welcome
      document.getElementById('welcomeSection').classList.add('active');
      mainMenu.classList.remove('active'); hamburgerBtn.classList.remove('active');
      newsPanel.classList.remove('open');
      window.scrollTo({top:0,behavior:'smooth'});
    });
  });

  // show welcome by default
  document.getElementById('welcomeSection').classList.add('active');

  /* ---------- Accessibility: close panels when clicking outside ---------- */
  document.addEventListener('click', (e)=>{
    const target = e.target;
    // close main menu if click outside
    if(!target.closest('.main-menu') && !target.closest('.hamburger')) {
      mainMenu.classList.remove('active'); hamburgerBtn.classList.remove('active');
    }
    // close news panel if click outside and news panel open and not clicking its button
    if(newsPanel.classList.contains('open') && !target.closest('.news-panel') && !target.closest('#newsBtn')){
      newsPanel.classList.remove('open');
    }
  });

  /* ---------- Notes ----------
   - Feed is offline sample data embedded above.
   - To use live feeds later, replace news-track contents dynamically via fetch() and rebuild track twice
   - Auto-scroll speed controlled by CSS animation duration (36s). Adjust for faster/slower.
  ------------------------------ */
</script>
</body>
</html>