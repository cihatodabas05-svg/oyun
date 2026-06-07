<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<title>Çağlar Boyu Savaş — TSK vs Düşman</title>
<style>
*{margin:0;padding:0;box-sizing:border-box}
body{background:#000;overflow:hidden;font-family:'Segoe UI',Arial,sans-serif;user-select:none}
#root{position:relative;width:1200px;height:600px;margin:0 auto;overflow:hidden}
#root canvas{position:absolute;top:0;left:0;z-index:0}
/* HUD */
#hud{position:absolute;top:0;left:0;right:0;height:54px;background:linear-gradient(180deg,rgba(4,8,20,.96),transparent);display:flex;align-items:center;padding:0 10px;gap:7px;z-index:20;pointer-events:none}
.hp{display:flex;align-items:center;gap:5px;background:rgba(0,0,0,.55);border:1px solid rgba(255,255,255,.12);border-radius:7px;padding:4px 9px}
.hlbl{color:#7a8fa8;font-size:9px;text-transform:uppercase;letter-spacing:1px}
.hbar{width:100px;height:7px;background:rgba(255,255,255,.1);border-radius:4px;overflow:hidden}
.hfill{height:100%;border-radius:4px;transition:width .2s}
#fphp{background:linear-gradient(90deg,#e74c3c,#ff6b6b)}
#fehp{background:linear-gradient(90deg,#e67e22,#f39c12)}
#fxp{background:linear-gradient(90deg,#27ae60,#2ecc71)}
.hval{color:#eee;font-size:12px;font-weight:700;min-width:38px}
.hsp{width:1px;height:26px;background:rgba(255,255,255,.1);margin:0 2px}
.hflex{flex:1}
/* Age */
#age-disp{position:absolute;top:56px;left:50%;transform:translateX(-50%);text-align:center;z-index:20;pointer-events:none;text-shadow:0 2px 6px rgba(0,0,0,.9)}
#age-yr{font-size:20px;font-weight:900;color:#f39c12;letter-spacing:3px}
#age-nm{font-size:10px;color:#aaa;letter-spacing:2px;margin-top:-2px}
/* Spawn panel */
#spawn-panel{position:absolute;bottom:0;left:0;right:0;height:88px;background:linear-gradient(0deg,rgba(4,8,20,.97),transparent);display:flex;align-items:flex-end;justify-content:center;padding:0 0 7px;gap:4px;z-index:20}
.sbtn{background:rgba(8,18,38,.88);border:1px solid rgba(70,110,170,.4);color:#cde;padding:5px 7px;border-radius:7px;cursor:pointer;font-size:9px;text-align:center;transition:all .14s;min-width:70px;max-width:78px;pointer-events:all;display:flex;flex-direction:column;gap:1px}
.sbtn:hover:not(.locked){background:rgba(25,55,95,.9);border-color:rgba(90,150,220,.7);transform:translateY(-2px)}
.sbtn.locked{opacity:.3;cursor:not-allowed}
.sbtn .sn{font-size:9px;font-weight:700;color:#ddf;line-height:1.2}
.sbtn .sc{font-size:11px;font-weight:800;color:#f5a623}
.sbtn .sh{font-size:8px;color:#7fa}
.sbtn .st{font-size:8px;color:#88a}
.sbtn.air{border-color:rgba(80,160,255,.4)}
.sbtn.tur{border-color:rgba(255,180,50,.5)}
/* Action buttons */
#actb{position:absolute;top:8px;right:8px;z-index:25;display:flex;flex-direction:column;gap:4px;pointer-events:all}
/* Komuta toggle butonu */
#btn-cmd-toggle{padding:7px 13px;border-radius:8px;border:1px solid rgba(255,180,60,.5);background:linear-gradient(135deg,#3a2a0a,#5a4010);color:#f5a623;font-size:11px;font-weight:800;cursor:pointer;letter-spacing:.5px;transition:all .15s;white-space:nowrap}
#btn-cmd-toggle:hover{background:linear-gradient(135deg,#5a4010,#7a5818);box-shadow:0 0 10px rgba(245,166,35,.4)}
#btn-cmd-toggle.open{background:linear-gradient(135deg,#5a1010,#8a2018);color:#ff8866;border-color:rgba(255,100,60,.5)}
/* Komuta çekmecesi */
#cmd-drawer{position:absolute;top:8px;right:130px;z-index:24;background:rgba(8,12,20,.92);border:1px solid rgba(255,180,60,.25);border-radius:12px;padding:10px 12px;display:flex;flex-direction:column;gap:7px;pointer-events:all;
  transform:translateX(20px) scaleX(0.85);transform-origin:right center;opacity:0;pointer-events:none;
  transition:opacity .2s ease,transform .2s ease;max-width:340px;backdrop-filter:blur(6px)}
#cmd-drawer.open{transform:translateX(0) scaleX(1);opacity:1;pointer-events:all}
.cmd-section-label{font-size:8px;color:#f5a62388;letter-spacing:2px;font-weight:800;text-transform:uppercase;padding:0 2px;margin-bottom:-3px}
.cmd-row{display:flex;flex-direction:row;gap:4px;flex-wrap:wrap}
.abtn{padding:6px 11px;border-radius:7px;border:none;cursor:pointer;font-size:10px;font-weight:700;letter-spacing:.3px;transition:all .14s;white-space:nowrap}
.abtn.upg{background:linear-gradient(135deg,#1a5c2a,#27ae60);color:#fff;border:1px solid #2ecc71}
.abtn.upg:hover{background:linear-gradient(135deg,#27ae60,#2ecc71)}
.abtn.ult{background:linear-gradient(135deg,#6a0a0a,#aa1818);color:#fff;border:1px solid #cc3333;position:relative;overflow:hidden}
.abtn.ult.ready{background:linear-gradient(135deg,#aa0000,#ff2200);border-color:#ff5533;animation:ultP 1.1s ease-in-out infinite}
.abtn.ult:disabled{opacity:.55;cursor:not-allowed}
@keyframes ultP{0%,100%{box-shadow:0 0 6px rgba(255,60,30,.5)}50%{box-shadow:0 0 20px rgba(255,80,30,.95)}}
@keyframes upgReady{0%,100%{box-shadow:0 0 6px rgba(46,204,113,.4)}50%{box-shadow:0 0 22px rgba(46,204,113,.95)}}
.abtn.upg.glow{animation:upgReady .85s ease-in-out infinite;border-color:#2ecc71!important}
.ult-bar-wrap{position:absolute;bottom:0;left:0;right:0;height:3px;background:rgba(0,0,0,.4)}
.ult-bar{height:3px;background:linear-gradient(90deg,#ff4400,#ffaa00);transition:width .25s linear;width:0%}
.abtn.qz{background:linear-gradient(135deg,#4a1277,#8e44ad);color:#fff;border:1px solid #9b59b6}
.abtn.qz:hover:not(:disabled){background:linear-gradient(135deg,#6c1db5,#9b59b6)}
.abtn.qz:disabled{opacity:.45;cursor:not-allowed}
/* ── ARAŞTIRMA AĞACI ─── */
#research-btn{padding:7px 13px;border-radius:8px;border:1px solid rgba(100,200,255,.45);background:linear-gradient(135deg,#0a2a4a,#1a4a7a);color:#88ddff;font-size:11px;font-weight:800;cursor:pointer;letter-spacing:.5px;transition:all .15s;white-space:nowrap;width:100%}
#research-btn:hover{background:linear-gradient(135deg,#1a4a7a,#2a6aaa);box-shadow:0 0 10px rgba(100,200,255,.35)}
#research-panel{position:absolute;top:8px;right:270px;z-index:30;background:rgba(4,10,22,.96);border:1px solid rgba(100,200,255,.25);border-radius:14px;padding:14px;width:360px;
  transform:translateX(-20px);opacity:0;pointer-events:none;transition:opacity .2s ease,transform .2s ease;backdrop-filter:blur(8px)}
#research-panel.open{transform:translateX(0);opacity:1;pointer-events:all}
.res-title{font-size:13px;font-weight:900;color:#88ddff;letter-spacing:3px;text-align:center;margin-bottom:10px;text-shadow:0 0 12px rgba(100,200,255,.5)}
.res-tabs{display:flex;gap:4px;margin-bottom:10px}
.res-tab{flex:1;padding:5px;border-radius:6px;border:1px solid rgba(255,255,255,.15);background:rgba(255,255,255,.05);color:#888;font-size:9px;font-weight:700;cursor:pointer;text-align:center;transition:all .15s;letter-spacing:1px}
.res-tab.active{background:rgba(100,200,255,.18);border-color:rgba(100,200,255,.5);color:#88ddff}
.res-grid{display:flex;flex-direction:column;gap:6px}
.res-item{display:flex;align-items:center;gap:8px;padding:7px 10px;border-radius:8px;border:1px solid rgba(255,255,255,.1);background:rgba(255,255,255,.04);cursor:pointer;transition:all .15s}
.res-item:hover:not(.owned){background:rgba(100,200,255,.1);border-color:rgba(100,200,255,.35)}
.res-item.owned{background:rgba(46,204,113,.1);border-color:rgba(46,204,113,.35);cursor:default}
.res-item.locked{opacity:.4;cursor:not-allowed}
.res-icon{font-size:20px;flex-shrink:0}
.res-info{flex:1}
.res-name{font-size:10px;font-weight:800;color:#ddd;margin-bottom:2px}
.res-desc{font-size:8px;color:#888;line-height:1.4}
.res-cost{font-size:10px;font-weight:800;color:#f5a623;flex-shrink:0}
.res-item.owned .res-cost{color:#2ecc71}
/* ── DALGA ÖNİZLEME ─── */
#wave-preview{position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);z-index:60;background:rgba(4,8,20,.96);border:2px solid rgba(255,60,60,.5);border-radius:16px;padding:20px 28px;text-align:center;pointer-events:none;opacity:0;transition:opacity .3s;min-width:280px}
#wave-preview.show{opacity:1}
#wave-preview .wp-title{font-size:11px;color:#ff8844;letter-spacing:3px;font-weight:800;margin-bottom:6px}
#wave-preview .wp-wave{font-size:42px;font-weight:900;color:#ff3300;line-height:1;text-shadow:0 0 20px #ff4400;margin-bottom:4px}
#wave-preview .wp-units{font-size:11px;color:#ccc;margin-bottom:10px}
#wave-preview .wp-bar-wrap{height:4px;background:rgba(255,255,255,.1);border-radius:2px;overflow:hidden}
#wave-preview .wp-bar{height:100%;background:linear-gradient(90deg,#ff3300,#ff8800);border-radius:2px;transition:width .1s linear}
/* ── DEMİR PERDE ─── */
#iron-curtain-flash{position:absolute;inset:0;pointer-events:none;z-index:14;background:rgba(0,80,180,.3);opacity:0;transition:opacity .3s}
#iron-curtain-flash.active{opacity:1;animation:icPulse .6s ease-in-out infinite}
@keyframes icPulse{0%,100%{background:rgba(0,80,180,.18)}50%{background:rgba(0,120,255,.42)}}
/* ── TIKLAMALı TOPÇU ─── */
#artillery-cursor{position:absolute;pointer-events:none;z-index:35;display:none}
#artillery-cursor::before{content:'🎯';font-size:24px;position:absolute;transform:translate(-50%,-50%)}
/* ── DİNAMİK GÖK ─── */
/* (canvas üzerinde JS ile çizilir) */
/* ── YENİ BUTONLAR ─── */
.abtn.ic{background:linear-gradient(135deg,#0a1a4a,#1a2a8a);color:#88aaff;border:1px solid rgba(80,120,255,.5)}
.abtn.ic.ready{background:linear-gradient(135deg,#1a2a8a,#2a4aff);border-color:rgba(100,150,255,.9);animation:icReady .8s ease-in-out infinite}
@keyframes icReady{0%,100%{box-shadow:0 0 6px rgba(80,120,255,.4)}50%{box-shadow:0 0 18px rgba(80,160,255,.9)}}
/* ── SKOR TABLO GELIŞTIRME ─── */
.stat-card{background:rgba(255,255,255,.06);border:1px solid rgba(255,255,255,.1);border-radius:10px;padding:14px;text-align:center;transition:all .2s}
.stat-card:hover{background:rgba(255,255,255,.1);border-color:rgba(255,255,255,.25)}
/* Toasts */
#toasts{position:absolute;top:95px;left:50%;transform:translateX(-50%);z-index:30;pointer-events:none;display:flex;flex-direction:column;align-items:center;gap:3px}
.toast{background:rgba(0,0,0,.85);border:1px solid rgba(255,255,255,.18);color:#fff;padding:4px 14px;border-radius:18px;font-size:11px;font-weight:600;animation:ta 2.8s forwards}
@keyframes ta{0%{opacity:0;transform:translateY(6px) scale(.9)}12%{opacity:1;transform:translateY(0) scale(1)}72%{opacity:1}100%{opacity:0;transform:translateY(-12px)}}
/* Quiz */
#qmodal{display:none;position:fixed;inset:0;background:rgba(0,0,0,.88);z-index:100;justify-content:center;align-items:center}
#qmodal.open{display:flex}
.qcard{background:linear-gradient(160deg,#0d1b2a,#1a2c3d);border:2px solid #3d6b9e;border-radius:13px;padding:26px;max-width:510px;width:92%;color:#ecf0f1;box-shadow:0 20px 60px rgba(0,0,0,.7)}
.qtit{font-size:13px;color:#f39c12;font-weight:800;text-align:center;margin-bottom:3px;letter-spacing:1px}
.qrh{font-size:10px;color:#7fa;text-align:center;margin-bottom:16px}
.qq{font-size:14px;line-height:1.65;margin-bottom:18px;color:#dde;border-left:3px solid #3d6b9e;padding-left:11px}
.qopts{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.qopt{background:rgba(255,255,255,.07);border:1px solid rgba(255,255,255,.17);color:#ccc;padding:10px 12px;border-radius:8px;cursor:pointer;font-size:11px;text-align:left;transition:all .17s;display:flex;align-items:center;gap:7px}
.qopt:hover:not(.answered){background:rgba(61,107,158,.35);border-color:#5d8ec5;color:#fff}
.qopt.correct{background:rgba(39,174,96,.35)!important;border-color:#27ae60!important;color:#7dffac!important}
.qopt.wrong{background:rgba(231,76,60,.35)!important;border-color:#e74c3c!important;color:#f88!important}
.qltr{width:20px;height:20px;border-radius:50%;background:rgba(255,255,255,.1);display:flex;align-items:center;justify-content:center;font-size:10px;font-weight:700;flex-shrink:0}
.qres{text-align:center;margin-top:14px;font-size:14px;font-weight:700;min-height:20px}
.qcb{display:none;margin-top:13px;width:100%;padding:11px;background:linear-gradient(135deg,#1a5c2a,#27ae60);border:none;color:#fff;border-radius:8px;cursor:pointer;font-size:12px;font-weight:700}
.qcb.show{display:block}
/* Quiz timer */
.qtimer-wrap{display:flex;justify-content:center;margin-bottom:12px}
#qtimer{width:48px;height:48px;border-radius:50%;background:rgba(255,255,255,.06);border:3px solid #3d6b9e;display:flex;align-items:center;justify-content:center;font-size:19px;font-weight:900;color:#f0c040;transition:color .3s,border-color .3s}
#qtimer.urgent{border-color:#e74c3c;color:#e74c3c;animation:timerPulse .45s ease-in-out infinite}
#qtimer.expired{border-color:#e74c3c!important;color:#e74c3c!important;animation:none}
@keyframes timerPulse{0%,100%{transform:scale(1)}50%{transform:scale(1.14)}}
/* End screen */
#endscreen{display:none;position:fixed;inset:0;background:rgba(0,0,0,.92);z-index:200;justify-content:center;align-items:center}
#endscreen.show{display:flex}
.ecard{text-align:center}
.etit{font-size:52px;font-weight:900;margin-bottom:8px;letter-spacing:4px}
.etit.win{color:#2ecc71;text-shadow:0 0 30px rgba(46,204,113,.6)}
.etit.lose{color:#e74c3c;text-shadow:0 0 30px rgba(231,76,60,.6)}
.esub{font-size:17px;color:#aaa;margin-bottom:28px}
.erb{padding:13px 44px;background:linear-gradient(135deg,#1a5c2a,#27ae60);border:none;color:#fff;border-radius:9px;font-size:14px;font-weight:700;cursor:pointer}
/* Danger vignette */
#danger-vignette{position:absolute;inset:0;pointer-events:none;z-index:15;}
@keyframes dangerP{0%,100%{box-shadow:inset 0 0 65px rgba(255,0,0,.28)}50%{box-shadow:inset 0 0 120px rgba(255,0,0,.7)}}
#danger-vignette.active{animation:dangerP .6s ease-in-out infinite;}
/* Speed button */
.abtn.spd{background:rgba(15,35,15,.88);color:#8c8;border:1px solid rgba(50,140,50,.4);font-size:10px}
.abtn.spd.fast{background:rgba(20,60,15,.95);color:#aff;border-color:rgba(70,200,50,.8);box-shadow:0 0 8px rgba(60,200,60,.4)}
/* Air support */
.abtn.asup{background:linear-gradient(135deg,#1a3a6a,#2a5a9a);color:#fff;border:1px solid #4a8acc}
.abtn.asup:hover:not(:disabled){background:linear-gradient(135deg,#2a5a9a,#4a8acc)}
.abtn.asup:disabled{opacity:.45;cursor:not-allowed}
/* AI ult dot */
#ai-ult-dot{display:inline-block;width:8px;height:8px;border-radius:50%;background:#555;margin-left:4px;vertical-align:middle;transition:background .3s}
#ai-ult-dot.ready{background:#ff4400;box-shadow:0 0 6px #ff4400}
/* Repair button */
.abtn.rep{background:linear-gradient(135deg,#0a3a5c,#1565c0);color:#fff;border:1px solid #2196f3}
.abtn.rep:hover:not(:disabled){background:linear-gradient(135deg,#1565c0,#2196f3)}
.abtn.rep:disabled{opacity:.45;cursor:not-allowed}
/* Sound button */
.abtn.snd{background:rgba(20,20,40,.8);color:#aaa;border:1px solid rgba(100,100,150,.35);font-size:11px}
.abtn.snd:hover{background:rgba(40,40,70,.9);color:#ddd}
/* Hotkey hints on spawn buttons */
.sbtn .skh{font-size:7px;color:#445;margin-top:1px;letter-spacing:.5px}
/* End screen stats */
.estats{display:flex;gap:10px;margin:14px 0;justify-content:center;flex-wrap:wrap}
.estat{background:rgba(255,255,255,.05);border:1px solid rgba(255,255,255,.1);border-radius:8px;padding:9px 16px;text-align:center}
.estat-v{font-size:22px;font-weight:900;color:#f39c12}
.estat-l{font-size:9px;color:#888;text-transform:uppercase;letter-spacing:1px;margin-top:2px}
/* Difficulty screen */
#diff-screen{position:fixed;inset:0;background:rgba(0,0,0,.97);z-index:500;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:20px}
.diff-title{font-size:36px;font-weight:900;color:#f39c12;letter-spacing:5px;text-align:center;text-shadow:0 0 30px rgba(243,156,18,.4)}
.diff-ver{font-size:10px;color:#555;letter-spacing:3px;margin-top:-16px}
.diff-sub{font-size:13px;color:#7a8fa8;letter-spacing:2px}
.diff-btns{display:flex;gap:18px;margin-top:4px}
.diff-btn{padding:16px 36px;border-radius:12px;border:2px solid;cursor:pointer;font-size:14px;font-weight:900;letter-spacing:1.5px;transition:all .18s;background:transparent;font-family:inherit}
.diff-btn.easy{border-color:#27ae60;color:#2ecc71}
.diff-btn.easy:hover{background:rgba(39,174,96,.2);transform:translateY(-3px);box-shadow:0 8px 24px rgba(39,174,96,.3)}
.diff-btn.normal{border-color:#f39c12;color:#f39c12}
.diff-btn.normal:hover{background:rgba(243,156,18,.2);transform:translateY(-3px);box-shadow:0 8px 24px rgba(243,156,18,.3)}
.diff-btn.hard{border-color:#e74c3c;color:#e74c3c}
.diff-btn.hard:hover{background:rgba(231,76,60,.2);transform:translateY(-3px);box-shadow:0 8px 24px rgba(231,76,60,.3)}
.diff-camp-btn{padding:9px 22px;border-radius:8px;border:1px solid #9b59b6;color:#9b59b6;background:transparent;cursor:pointer;font-size:12px;font-weight:700;transition:all .18s;font-family:inherit}
.diff-camp-btn:hover{background:rgba(155,89,182,.2)}
#hi-scores-list{text-align:center;color:#555;font-size:10px;line-height:1.9;margin-top:-8px}
/* Stats panel */
#stats-panel{display:none;position:absolute;inset:0;background:rgba(0,0,0,.9);z-index:50;flex-direction:column;align-items:center;justify-content:center;gap:14px}
#stats-panel.open{display:flex}
.stats-title{font-size:18px;font-weight:900;color:#f39c12;letter-spacing:3px}
.stats-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;width:660px}
.stat-card{background:rgba(255,255,255,.06);border:1px solid rgba(255,255,255,.1);border-radius:10px;padding:14px;text-align:center}
.stat-card .sv{font-size:26px;font-weight:900;color:#fff}
.stat-card .sl{font-size:8px;color:#666;text-transform:uppercase;letter-spacing:1.5px;margin-top:4px}
.stats-close-hint{font-size:10px;color:#444}
/* Achievement */
#achiev-wrap{position:absolute;bottom:100px;left:50%;transform:translateX(-50%);z-index:35;pointer-events:none;display:flex;flex-direction:column;align-items:center;gap:6px}
.achiev{background:linear-gradient(135deg,#111e11,#0a2a0a);border:1px solid #27ae60;border-radius:10px;padding:10px 20px;color:#fff;display:flex;align-items:center;gap:10px;animation:achievAnim 4s forwards;min-width:240px}
@keyframes achievAnim{0%{opacity:0;transform:translateY(16px)}12%{opacity:1;transform:translateY(0)}80%{opacity:1}100%{opacity:0;transform:translateY(-16px)}}
.achiev-icon{font-size:26px}
.achiev-title{font-size:11px;font-weight:800;color:#2ecc71;letter-spacing:1px}
.achiev-desc{font-size:9px;color:#777;margin-top:2px}
/* Frontline */
#frontline-wrap{position:absolute;top:54px;left:0;right:0;height:5px;background:rgba(0,0,0,.45);z-index:20;pointer-events:none}
#frontline-p{position:absolute;top:0;left:0;height:100%;background:linear-gradient(90deg,#1a6a30,#27ae60);transition:width .5s}
#frontline-e{position:absolute;top:0;right:0;height:100%;background:linear-gradient(90deg,#c0392b,#e74c3c);transition:width .5s}
#frontline-mid{position:absolute;top:-3px;width:4px;height:11px;background:#fff;border-radius:2px;transform:translateX(-50%);transition:left .5s;box-shadow:0 0 6px rgba(255,255,255,.7)}
/* Mine button */
.abtn.mine{background:linear-gradient(135deg,#5c3d0a,#8a5e0a);color:#ffd;border:1px solid #c8860b}
.abtn.mine:hover:not(:disabled){background:linear-gradient(135deg,#8a5e0a,#c8860b)}
.abtn.mine:disabled{opacity:.45;cursor:not-allowed}
/* Cinematic overlay */
#cinematic{position:absolute;inset:0;background:linear-gradient(180deg,rgba(0,0,0,.82),rgba(0,0,0,.45));z-index:180;display:flex;flex-direction:column;align-items:center;justify-content:center;pointer-events:none;transition:opacity 1.2s}
.cin-year{font-size:64px;font-weight:900;color:#f39c12;letter-spacing:10px;text-shadow:0 0 50px rgba(243,156,18,.7)}
.cin-name{font-size:15px;color:#7a8fa8;letter-spacing:6px;margin-top:-6px}
.cin-sub{font-size:10px;color:#444;margin-top:20px;letter-spacing:4px}
/* Streak display */
#streak-display{position:absolute;top:72px;left:50%;transform:translateX(-50%);z-index:22;pointer-events:none;text-align:center;font-weight:900;font-size:18px;color:#ff8800;text-shadow:0 0 12px rgba(255,136,0,.7);opacity:0;transition:opacity .3s;letter-spacing:2px}
#streak-display.visible{opacity:1}
/* Campaign screen */
#campaign-screen{display:none;position:fixed;inset:0;background:rgba(0,0,0,.97);z-index:400;flex-direction:column;align-items:center;justify-content:center;gap:20px}
#campaign-screen.open{display:flex}
.camp-num{font-size:11px;color:#7a8fa8;letter-spacing:4px}
.camp-title-big{font-size:42px;font-weight:900;color:#f39c12;letter-spacing:5px;text-shadow:0 0 30px rgba(243,156,18,.5)}
.camp-desc{font-size:13px;color:#aaa;text-align:center;max-width:500px;line-height:1.8}
.camp-btn{padding:14px 44px;background:linear-gradient(135deg,#1a5c2a,#27ae60);border:none;color:#fff;border-radius:9px;font-size:14px;font-weight:700;cursor:pointer;letter-spacing:1px;font-family:inherit;transition:all .2s}
.camp-btn:hover{transform:translateY(-2px);box-shadow:0 6px 20px rgba(39,174,96,.4)}
/* End screen ribbon */
.erib{font-size:11px;color:#f39c12;letter-spacing:2px;margin-bottom:6px;opacity:.8}
.erb2{padding:10px 30px;background:transparent;border:1px solid #666;color:#888;border-radius:9px;font-size:12px;font-weight:700;cursor:pointer;font-family:inherit;margin-top:4px;transition:all .2s}
.erb2:hover{border-color:#aaa;color:#ccc}
/* EMP butonu */
.abtn.emp{background:linear-gradient(135deg,#0a2a4a,#1a4a7a);color:#7af;border:1px solid #2a7acc}
.abtn.emp:hover:not(:disabled){background:linear-gradient(135deg,#1a4a7a,#2a7acc)}
.abtn.emp:disabled{opacity:.45;cursor:not-allowed}
/* Topçu v2 */
.abtn.arty{background:linear-gradient(135deg,#3a1a0a,#6a3a0a);color:#ffa;border:1px solid #aa7a00}
.abtn.arty:hover:not(:disabled){background:linear-gradient(135deg,#6a3a0a,#aa7a00)}
.abtn.arty:disabled{opacity:.45;cursor:not-allowed}
/* Gece modu */
#night-overlay{position:absolute;inset:0;background:rgba(0,0,30,.0);pointer-events:none;z-index:7;transition:background .8s}
#night-overlay.active{background:rgba(0,0,30,.45)}
/* Mini harita */
#minimap{position:absolute;bottom:96px;right:6px;width:120px;height:50px;background:rgba(0,0,0,.7);border:1px solid rgba(255,255,255,.15);border-radius:5px;z-index:22;pointer-events:none}
/* Savaş günlüğü */
#combat-log{position:absolute;top:60px;left:6px;width:200px;z-index:21;pointer-events:none}
.log-entry{font-size:8px;color:#aaa;padding:1px 5px;background:rgba(0,0,0,.5);border-radius:3px;margin-bottom:1px;animation:logFade 8s forwards}
@keyframes logFade{0%{opacity:1}70%{opacity:.8}100%{opacity:0}}
/* Panik modu */
@keyframes panicEdge{0%,100%{box-shadow:inset 0 0 50px rgba(255,80,0,.2)}50%{box-shadow:inset 0 0 90px rgba(255,80,0,.65)}}
#panic-overlay{position:absolute;inset:0;pointer-events:none;z-index:16;}
#panic-overlay.active{animation:panicEdge .4s ease-in-out infinite}
/* Faiz göstergesi */
#interest-toast{position:absolute;top:62px;right:160px;z-index:22;font-size:10px;color:#f5a623;font-weight:700;pointer-events:none;opacity:0;transition:opacity .3s}
#interest-toast.pop{opacity:1}
/* Gece/Gündüz butonu */
.abtn.night{background:rgba(5,5,25,.9);color:#88aaff;border:1px solid rgba(80,100,200,.4)}
.abtn.night.day{background:rgba(25,20,5,.9);color:#ffcc44;border:1px solid rgba(200,160,50,.4)}
/* ESC Duraklat Menüsü */
#pause-menu{position:absolute;inset:0;background:rgba(0,0,0,.72);z-index:80;display:none;flex-direction:column;align-items:center;justify-content:center;gap:14px}
#pause-menu.open{display:flex}
#pause-menu h2{color:#fff;font-size:28px;letter-spacing:4px;margin:0 0 10px}
.pmenu-btn{background:linear-gradient(135deg,#1a2a3a,#0d1620);color:#eee;border:1px solid rgba(255,255,255,.2);padding:12px 44px;border-radius:8px;font-size:15px;cursor:pointer;letter-spacing:2px;transition:all .2s;min-width:200px}
.pmenu-btn:hover{background:linear-gradient(135deg,#2a3a5a,#1a2640);color:#fff;border-color:rgba(100,160,255,.5)}
/* Yardım Paneli */
#help-overlay{position:absolute;inset:0;background:rgba(0,0,0,.82);z-index:85;display:none;flex-direction:column;align-items:center;justify-content:center}
#help-overlay.open{display:flex}
#help-box{background:#0d1420;border:1px solid rgba(255,255,255,.18);border-radius:12px;padding:18px 28px;max-width:420px;color:#ccc;font-size:12px;line-height:1.8}
#help-box h3{color:#88aaff;margin:0 0 10px;font-size:16px;letter-spacing:2px}
#help-box kbd{background:#1a2a3a;border:1px solid #334;border-radius:4px;padding:1px 6px;font-size:11px;color:#aac}
#help-close{margin-top:14px;padding:8px 32px;background:#1a2a44;color:#aac;border:1px solid #334;border-radius:6px;cursor:pointer;font-size:12px}
/* Dalga Uyarı Overlay */
#wave-alert{position:absolute;top:30%;left:50%;transform:translateX(-50%);pointer-events:none;z-index:50;font-size:42px;font-weight:900;letter-spacing:6px;color:#ff2200;text-shadow:0 0 30px #ff0000,0 0 60px #ff4400;opacity:0;transition:opacity .3s}
/* Hover Tooltip */
#unit-tooltip{position:absolute;background:rgba(0,0,20,.9);border:1px solid rgba(100,150,255,.35);border-radius:6px;padding:6px 10px;font-size:10px;color:#cce;pointer-events:none;z-index:60;display:none;line-height:1.6;min-width:130px}
/* Kill Kutlama */
#kill-celebrate{position:absolute;top:38%;left:50%;transform:translateX(-50%);pointer-events:none;z-index:55;font-size:36px;font-weight:900;color:#f5a623;text-shadow:0 0 20px #ffaa00;opacity:0;transition:opacity .2s;white-space:nowrap}
/* Bölge Kontrolü Bayrak */
#zone-flag{position:absolute;bottom:48px;left:50%;transform:translateX(-50%);pointer-events:none;z-index:25;font-size:22px;transition:all .5s}
/* Skor Çarpanı */
#score-mult{position:absolute;top:58px;left:50%;transform:translateX(-50%);pointer-events:none;z-index:26;font-size:11px;color:#f5a623;font-weight:700;opacity:0;transition:opacity .4s}
#score-mult.show{opacity:1}
/* Başarım Sayacı */
#ach-count{position:absolute;bottom:50px;left:6px;font-size:9px;color:#888;z-index:22;pointer-events:none}
/* Drone butonu */
.abtn.drone{background:linear-gradient(135deg,#0a2a4a,#1a4a6a);color:#44aaff;border:1px solid rgba(68,170,255,.3)}
.abtn.drone:hover:not(:disabled){background:linear-gradient(135deg,#1a3a5a,#2a6a9a)}
/* Sis bombası butonu */
.abtn.fog{background:linear-gradient(135deg,#2a2a2a,#4a4a5a);color:#aaaacc;border:1px solid rgba(150,150,200,.3)}
.abtn.fog:hover:not(:disabled){background:linear-gradient(135deg,#3a3a4a,#5a5a7a)}
/* Nükleer butonu */
.abtn.nuke{background:linear-gradient(135deg,#3a0000,#600000);color:#ff4444;border:1px solid rgba(255,50,50,.4)}
.abtn.nuke:hover:not(:disabled){background:linear-gradient(135deg,#550000,#880000)}
/* Lojistik butonu */
.abtn.logi{background:linear-gradient(135deg,#003a1a,#005a2a);color:#44ee88;border:1px solid rgba(68,200,100,.3)}
.abtn.logi:hover:not(:disabled){background:linear-gradient(135deg,#005a2a,#008040)}
/* Sabotaj butonu */
.abtn.sabo{background:linear-gradient(135deg,#2a1a00,#4a3a00);color:#ffaa00;border:1px solid rgba(255,170,0,.3)}
.abtn.sabo:hover:not(:disabled){background:linear-gradient(135deg,#3a2800,#605000)}
/* Bölge kontrolü göstergesi */
#zone-bar{position:absolute;bottom:38px;left:50%;transform:translateX(-55%);width:120px;height:6px;background:rgba(0,0,0,.5);border-radius:3px;z-index:23;border:1px solid rgba(255,255,255,.15)}
#zone-fill{height:100%;width:50%;background:linear-gradient(90deg,#27ae60,#2ecc71);border-radius:3px;transition:width .5s,background .5s}
/* Sis efekti overlay */
#fog-overlay{position:absolute;inset:0;background:rgba(200,200,220,0);pointer-events:none;z-index:8;transition:background 1s}
#fog-overlay.active{background:rgba(200,200,220,.18)}
/* Tarihi Olay Kartı */
#event-card{position:absolute;top:18%;left:50%;transform:translateX(-50%);z-index:72;background:linear-gradient(135deg,rgba(10,18,30,.97),rgba(20,30,50,.97));border:2px solid rgba(255,200,50,.5);border-radius:14px;padding:18px 28px;min-width:320px;max-width:420px;text-align:center;pointer-events:none;opacity:0;transition:opacity .4s;box-shadow:0 0 40px rgba(255,180,0,.2)}
#event-card.show{opacity:1}
#event-icon{font-size:40px;margin-bottom:6px}
#event-title{font-size:16px;font-weight:900;color:#f5a623;letter-spacing:2px;margin-bottom:4px}
#event-desc{font-size:11px;color:#ccc;line-height:1.6}
#event-effect{font-size:13px;font-weight:700;color:#44ee88;margin-top:8px}
/* Kill Feed */
#kill-feed{position:absolute;right:6px;top:110px;width:180px;pointer-events:none;z-index:24;overflow:hidden;max-height:180px}
.kf-entry{font-size:10px;color:#eee;background:rgba(0,0,0,.6);border-left:3px solid #f5a623;padding:3px 6px;margin-bottom:2px;border-radius:0 4px 4px 0;opacity:1;transition:opacity 1s;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.kf-entry.p-kill{border-left-color:#44ee88}
.kf-entry.e-kill{border-left-color:#ff4444}
/* Taret Yükseltme - çekmeceye taşındı */
/* Birim hasar yanıp sönme (canvas overlay değil — JS tween) */
/* EMP efekti */
#emp-ring{position:absolute;pointer-events:none;z-index:18;display:none}
</style>
</head>
<body>
<!-- Zorluk Seçici -->
<div id="diff-screen">
  <div class="diff-title">⚔ ÇAĞLAR BOYU SAVAŞ</div>
  <div class="diff-ver">v3.0 — TAM SÜRÜM</div>
  <div class="diff-sub">Zorluk seviyesini seçin</div>
  <div class="diff-btns">
    <button class="diff-btn easy" onclick="window._startGame&&window._startGame('easy')">🟢 KOLAY</button>
    <button class="diff-btn normal" onclick="window._startGame&&window._startGame('normal')">🟡 NORMAL</button>
    <button class="diff-btn hard" onclick="window._startGame&&window._startGame('hard')">🔴 ZOR</button>
  </div>
  <button class="diff-camp-btn" onclick="window._startCampaign&&window._startCampaign()">📋 Kampanya Modu (5 Görev)</button>
  <div id="hi-scores-list">Yüksek skorlar yükleniyor...</div>
</div>
<!-- Kampanya Ekranı -->
<div id="campaign-screen">
  <div class="camp-num" id="camp-num">GÖREV 1 / 5</div>
  <div class="camp-title-big" id="camp-title">ÇAĞLAR BOYU SAVAŞ</div>
  <div class="camp-desc" id="camp-desc">İlk görev başlıyor...</div>
  <button class="camp-btn" onclick="window._beginCampaignMission&&window._beginCampaignMission()">⚔ GÖREVE BAŞLA</button>
</div>
<div id="root">
  <div id="hud">
    <div class="hp">
      <span class="hlbl">TSK Üssü</span>
      <div class="hbar"><div class="hfill" id="fphp" style="width:100%"></div></div>
      <span class="hval" id="vphp">1000</span>
    </div>
    <div class="hp">
      <span style="font-size:12px">💰</span><span class="hlbl">Kasa</span>
      <span class="hval" id="vgold" style="color:#f5a623">500₺</span>
    </div>
    <div class="hp">
      <span class="hlbl">XP</span>
      <div class="hbar"><div class="hfill" id="fxp" style="width:0%"></div></div>
      <span class="hval" id="vxp" style="color:#2ecc71;min-width:65px">0/800</span>
    </div>
    <div class="hsp"></div>
    <div class="hp">
      <span style="font-size:11px">⚔</span><span class="hlbl">İmha</span>
      <span class="hval" id="vkills" style="color:#2ecc71">0</span>
      <span style="color:#444;font-size:10px;margin:0 2px">/</span>
      <span class="hval" id="vekills" style="color:#e74c3c">0</span>
    </div>
    <div class="hp">
      <span style="font-size:11px">🪖</span><span class="hlbl">Birlik</span>
      <span class="hval" id="vpunits" style="color:#2ecc71;font-size:10px">0</span>
      <span style="color:#444;font-size:9px;margin:0 1px">/</span>
      <span class="hval" id="veunits" style="color:#e74c3c;font-size:10px">0</span>
    </div>
    <div class="hp">
      <span class="hlbl">Gelir</span>
      <span class="hval" id="vinc" style="color:#f5a623;font-size:10px">+25₺/3s</span>
    </div>
    <div class="hp">
      <span style="font-size:11px">⚠</span><span class="hlbl">Dalga</span>
      <span class="hval" id="vwave" style="color:#ff8844;font-size:10px">28s</span>
    </div>
    <div class="hp">
      <span style="font-size:11px">💪</span><span class="hlbl">Moral</span>
      <span class="hval" id="vmorale" style="color:#f5a623;font-size:10px">70</span>
    </div>
    <div class="hsp"></div>
    <div class="hp" style="opacity:.75">
      <span style="font-size:11px">🤖</span><span class="hlbl">Düşman</span>
      <span class="hval" id="vaig" style="color:#9b59b6">500₺</span>
      <span id="ai-ult-dot" title="Düşman Özel Güç"></span>
    </div>
    <div class="hp">
      <span class="hval" id="vehp">1000</span>
      <div class="hbar"><div class="hfill" id="fehp" style="width:100%"></div></div>
      <span class="hlbl">Düşman Üssü</span>
    </div>
    <div class="hp" id="emp-status-row" style="display:none">
      <span style="font-size:10px;color:#44aaff">⚡EMP</span>
      <span class="hval" id="vEmpTimer" style="color:#44aaff;font-size:10px">0s</span>
    </div>
    <div class="hp" id="fog-status-row" style="display:none">
      <span style="font-size:10px;color:#aaaacc">🌫 SİS</span>
      <span class="hval" id="vFogTimer" style="color:#aaaacc;font-size:10px">0s</span>
    </div>
    <div class="hp" id="sabo-status-row" style="display:none">
      <span style="font-size:10px;color:#ffaa00">💸SAB</span>
      <span class="hval" id="vSaboTimer" style="color:#ffaa00;font-size:10px">0s</span>
    </div>
  </div>
  <div id="age-disp"><div id="age-yr">1990</div><div id="age-nm">SOĞUK SAVAŞ ÇAĞI</div></div>
  <div id="actb">
    <button class="abtn upg" id="btnUpg" onclick="window._tryUpgrade&&window._tryUpgrade()">⬆ Çağ Atla<br><small id="upgTxt" style="font-size:9px;font-weight:400">800 XP</small></button>
    <button class="abtn ult" id="btnUlt" onclick="window._fireUlt&&window._fireUlt()" disabled>⚡ %50 Özel Güç<br><small id="ultTxt" style="font-size:9px;font-weight:400">30s</small><div class="ult-bar-wrap"><div class="ult-bar" id="ultBar"></div></div></button>
    <button id="btn-cmd-toggle" onclick="window._toggleCmdDrawer&&window._toggleCmdDrawer()">🎮 KOMUTA ▾</button>
  </div>
  <!-- Komuta çekmecesi -->
  <div id="cmd-drawer">
    <div class="cmd-section-label">⚙ TEMEL</div>
    <div class="cmd-row">
      <button class="abtn rep" id="btnRep" onclick="window._repairBase&&window._repairBase()">🔧 Üs Onar<br><small id="repTxt" style="font-size:9px;font-weight:400">200₺/+%15</small></button>
      <button id="turret-upgrade-btn" class="abtn" onclick="window._upgradeTurret&&window._upgradeTurret()" style="background:linear-gradient(135deg,#1a3a5a,#0a2040);color:#44aaff;border:1px solid rgba(68,170,255,.35)">🏰 Kule Yükselt<br><small style="font-size:9px;font-weight:400">600₺</small></button>
      <button class="abtn qz" id="btnQuiz" onclick="window._openQuiz&&window._openQuiz()">❓ Bilgi Sorusu<br><small id="quizTxt" style="font-size:9px;font-weight:400"></small></button>
      <button class="abtn spd" id="btnSpd" onclick="window._toggleSpeed&&window._toggleSpeed()">⏩ Hız: 1×</button>
      <button class="abtn snd" id="btnStats" onclick="window._toggleStats&&window._toggleStats()">📊 İstatistik</button>
      <button class="abtn snd" id="btnHelp" onclick="window._toggleHelp&&window._toggleHelp()">❓ Yardım</button>
    </div>
    <div class="cmd-section-label">💥 SALDIRI</div>
    <div class="cmd-row">
      <button class="abtn asup" id="btnAirSup" onclick="window._airSupport&&window._airSupport()">✈ Hava<br><small id="airSupTxt" style="font-size:9px;font-weight:400">600₺</small></button>
      <button class="abtn arty" id="btnArty" onclick="window._heavyArty&&window._heavyArty()">💥 Topçu<br><small id="artyTxt" style="font-size:9px;font-weight:400">1200₺</small></button>
      <button class="abtn emp" id="btnEmp" onclick="window._empBlast&&window._empBlast()">⚡ EMP<br><small id="empTxt" style="font-size:9px;font-weight:400">500₺</small></button>
      <button class="abtn mine" id="btnMine" onclick="window._placeMine&&window._placeMine()">💣 Mayın<br><small id="mineTxt" style="font-size:9px;font-weight:400">150₺</small></button>
      <button class="abtn nuke" id="btnNuke" onclick="window._nuclearStrike&&window._nuclearStrike()">☢ Nükleer<br><small id="nukeTxt" style="font-size:9px;font-weight:400">5000₺</small></button>
    </div>
    <div class="cmd-section-label">🛠 DESTEK</div>
    <div class="cmd-row">
      <button class="abtn drone" id="btnDrone" onclick="window._droneSwarm&&window._droneSwarm()">🚁 Drone<br><small id="droneTxt" style="font-size:9px;font-weight:400">700₺</small></button>
      <button class="abtn fog" id="btnFog" onclick="window._fogBomb&&window._fogBomb()">🌫 Sis<br><small id="fogTxt" style="font-size:9px;font-weight:400">400₺</small></button>
      <button class="abtn logi" id="btnLogi" onclick="window._logisticSupport&&window._logisticSupport()">🏥 Lojistik<br><small id="logiTxt" style="font-size:9px;font-weight:400">500₺</small></button>
      <button class="abtn sabo" id="btnSabo" onclick="window._economicSabotage&&window._economicSabotage()">💸 Sabotaj<br><small id="saboTxt" style="font-size:9px;font-weight:400">800₺</small></button>
      <button class="abtn night" id="btnNight" onclick="window._toggleNight&&window._toggleNight()">🌙 Gece</button>
    </div>
    <div class="cmd-section-label">🔬 ARAŞTIRMA & ÖZEL</div>
    <div class="cmd-row">
      <button id="research-btn" onclick="window._toggleResearch&&window._toggleResearch()">🔬 Araştırma Ağacı</button>
    </div>
    <div class="cmd-row">
      <button class="abtn ic" id="btnIronCurtain" onclick="window._ironCurtain&&window._ironCurtain()">🛡 Demir Perde<br><small id="icTxt" style="font-size:9px;font-weight:400">60s CD</small></button>
      <button class="abtn" id="btnArtilleryMode" onclick="window._toggleArtilleryMode&&window._toggleArtilleryMode()" style="background:linear-gradient(135deg,#3a1a0a,#6a3010);color:#ffaa66;border:1px solid rgba(255,130,60,.4)">🎯 Topçu Nişan<br><small style="font-size:9px;font-weight:400">Haritaya tıkla</small></button>
    </div>
    <div class="cmd-section-label">🎯 AYARLAR</div>
    <div class="cmd-row">
      <button class="abtn" id="btnTarget" onclick="window._cycleTarget&&window._cycleTarget()" style="background:linear-gradient(135deg,#2a1a4a,#1a0a3a);color:#dd88ff;border-color:rgba(200,100,255,.35)">🎯 Hedef<br><small id="targetTxt" style="font-size:9px;font-weight:400">EN YAKIN</small></button>
      <button class="abtn snd" id="btnMusic" onclick="window._toggleMusic&&window._toggleMusic()">🎵 Müzik</button>
      <button class="abtn snd" id="btnSnd" onclick="window._toggleSound&&window._toggleSound()">🔊 Ses</button>
    </div>
  </div>
  <div id="night-overlay"></div>
  <div id="fog-overlay"></div>
  <div id="panic-overlay"></div>
  <div id="wave-alert">⚠ DÜŞMAN DALGASI!</div>
  <div id="kill-celebrate"></div>
  <div id="zone-flag">🚩</div>
  <div id="zone-bar"><div id="zone-fill"></div></div>
  <div id="score-mult"></div>
  <div id="ach-count"></div>
  <div id="unit-tooltip"></div>
  <div id="pause-menu">
    <h2>⏸ DURAKLATILDI</h2>
    <button class="pmenu-btn" onclick="window._resumeGame&&window._resumeGame()">▶ Devam Et</button>
    <button class="pmenu-btn" onclick="window._restartGame&&window._restartGame()">🔄 Yeniden Başlat</button>
    <button class="pmenu-btn" onclick="location.reload()">🏠 Ana Menü</button>
  </div>
  <div id="help-overlay">
    <div id="help-box">
      <h3>⌨ KLAVYE KISAYOLLARI</h3>
      <div><kbd>1-9</kbd> Birim üret</div>
      <div><kbd>Shift+Tıkla</kbd> 5× toplu satın al</div>
      <div><kbd>ESC</kbd> Duraklat / Devam</div>
      <div><kbd>R</kbd> Yeniden başlat</div>
      <div><kbd>P</kbd> Duraklat</div>
      <div><kbd>S</kbd> İstatistik paneli</div>
      <div><kbd>N</kbd> Gece modu toggle</div>
      <div><kbd>M</kbd> Müzik toggle</div>
      <div><kbd>Tab</kbd> Hız değiştir</div>
      <div style="margin-top:8px;color:#88aaff">Sağ Tık: Birimi sat (%50 iade)</div>
    </div>
    <button id="help-close" onclick="window._toggleHelp&&window._toggleHelp()">Kapat</button>
  </div>
  <div id="minimap"><canvas id="minimap-canvas" width="120" height="50"></canvas></div>
  <div id="combat-log"></div>
  <div id="interest-toast">💰 Faiz!</div>
  <div id="frontline-wrap"><div id="frontline-p" style="width:50%"></div><div id="frontline-e" style="width:50%"></div><div id="frontline-mid" style="left:50%"></div></div>
  <div id="weather-indicator" style="position:absolute;top:61px;right:145px;font-size:10px;color:#aac;z-index:22;pointer-events:none;text-shadow:0 1px 3px #000">☀ Açık</div>
  <div id="morale-bar-wrap" style="position:absolute;top:59px;left:0;width:200px;height:4px;background:rgba(0,0,0,.4);z-index:22;pointer-events:none"><div id="morale-bar-fill" style="height:100%;width:70%;background:linear-gradient(90deg,#27ae60,#f5a623);transition:width .5s,background .5s"></div></div>
  <div id="danger-vignette"></div>
  <div id="iron-curtain-flash"></div>
  <div id="wave-preview">
    <div class="wp-title">⚠ DÜŞMAN DALGASI GELİYOR</div>
    <div class="wp-wave" id="wp-wave-num">DALGA 1</div>
    <div class="wp-units" id="wp-units-desc">Hazırlanın!</div>
    <div class="wp-bar-wrap"><div class="wp-bar" id="wp-bar" style="width:100%"></div></div>
  </div>
  <div id="research-panel">
    <div class="res-title">🔬 ARAŞTIRMA AĞACI</div>
    <div class="res-tabs">
      <div class="res-tab active" onclick="window._resTab&&window._resTab('war')" id="rtab-war">⚔ SAVAŞ</div>
      <div class="res-tab" onclick="window._resTab&&window._resTab('def')" id="rtab-def">🛡 SAVUNMA</div>
      <div class="res-tab" onclick="window._resTab&&window._resTab('eco')" id="rtab-eco">💰 EKONOMİ</div>
    </div>
    <div class="res-grid" id="res-grid"></div>
  </div>
  <div id="cinematic"><div class="cin-year" id="cin-year">1990</div><div class="cin-name" id="cin-name">SOĞUK SAVAŞ ÇAĞI</div><div class="cin-sub">SAVAŞ BAŞLIYOR...</div></div>
  <div id="streak-display"></div>
  <div id="achiev-wrap"></div>
  <div id="event-card"><div id="event-icon">📜</div><div id="event-title">TARİHİ OLAY</div><div id="event-desc"></div><div id="event-effect"></div></div>
  <div id="kill-feed"></div>
  <div id="stats-panel">
    <div class="stats-title">📊 SAVAŞ İSTATİSTİKLERİ</div>
    <div class="stats-grid" id="stats-grid"></div>
    <div class="stats-close-hint">Tab veya S tuşu → kapat</div>
  </div>
  <div id="spawn-panel"></div>
  <div id="toasts"></div>
  <div id="qmodal">
    <div class="qcard">
      <div class="qtit">🎯 GENEL KÜLTÜR SORUSU</div>
      <div class="qtimer-wrap"><div id="qtimer">20</div></div>
      <div class="qrh" id="qrh">Doğru cevap için para ödülü!</div>
      <div class="qq" id="qqt"></div>
      <div class="qopts" id="qow"></div>
      <div class="qres" id="qres"></div>
      <button class="qcb" id="qcb" onclick="window._closeQuiz&&window._closeQuiz()">✓ Devam Et</button>
    </div>
  </div>
  <div id="endscreen">
    <div class="ecard">
      <div class="erib" id="end-rib"></div>
      <div class="etit" id="etit">ZAFER!</div>
      <div class="esub" id="esub">TSK düşmanı mağlup etti!</div>
      <div class="estats" id="end-stats"></div>
      <div id="end-score" style="font-size:28px;font-weight:900;color:#f39c12;margin:8px 0"></div>
      <div id="end-rank" style="font-size:11px;color:#888;margin-bottom:12px"></div>
      <button class="erb" onclick="location.reload()">🔄 Yeniden Oyna</button>
      <button class="erb2" id="end-camp-btn" onclick="window._continuecamp&&window._continuecamp()" style="display:none;margin-left:10px">➡ Sonraki Görev</button>
    </div>
  </div>
</div>
<script src="https://cdn.jsdelivr.net/npm/phaser@3.60.0/dist/phaser.min.js"></script>
<script>
// ═══════════════════════════════════════════
//  CONSTANTS
// ═══════════════════════════════════════════
const W=1200,H=600,GND=462,AIR_Y=185,PBX=90,EBX=1110,PSX=195,ESX=1005;
const ULT_CD=30000;
const MAX_TURRETS=3;
const MAX_UNITS_PER_SIDE=18;
const PDEF_LINE=340;   // Oyuncu savunma hattı X
const EDEF_LINE=860;   // Düşman savunma hattı X

// PNG asset overrides (boş bırakılan prosedürel çizime düşer)
const PNG_URLS={};

// ═══════════════════════════════════════════
//  AGE CONFIG
// ═══════════════════════════════════════════
const AGES=[
  {year:1990,name:'SOĞUK SAVAŞ ÇAĞI',  bHP:1000, xpN:420,  inc:25, xpInc:10},
  {year:2000,name:'MODERN SAVAŞ ÇAĞI', bHP:2500, xpN:950,  inc:40, xpInc:15},
  {year:2010,name:'TEKNOLOJİ ÇAĞI',    bHP:5000, xpN:1900, inc:60, xpInc:22},
  {year:2020,name:'DİJİTAL SAVAŞ ÇAĞI',bHP:8000, xpN:3500, inc:80, xpInc:30},
  {year:2030,name:'GELECEK SAVAŞI',    bHP:12000,xpN:99999,inc:100,xpInc:0}
];

// ═══════════════════════════════════════════
//  TURRET DEFS (per age — same for both sides)
// ═══════════════════════════════════════════
const TURRET_DEFS=[
  {age:0,name:'M2HB Savunma Kulesi',  cost:350, hp:80,  dmg:17, rng:270,rt:3000},
  {age:1,name:'ZU-23 Hava Savunma',   cost:550, hp:95,  dmg:21, rng:290,rt:3000},
  {age:2,name:'Oerlikon 35mm Kule',   cost:900, hp:110, dmg:27, rng:310,rt:3000},
  {age:3,name:'CIWS Phalanx Kule',    cost:1400,hp:130, dmg:33, rng:330,rt:3000},
  {age:4,name:'Lazer Savunma Kulesi', cost:2000,hp:150, dmg:46, rng:350,rt:3000},
];

// Turret slot positions (3 per base)
const PTUR_POS=[{x:PBX-28,y:GND-112},{x:PBX+8, y:GND-118},{x:PBX+44,y:GND-100}];
const ETUR_POS=[{x:EBX+28,y:GND-90}, {x:EBX-8, y:GND-95}, {x:EBX-44,y:GND-80}];

// ═══════════════════════════════════════════
//  QUIZ
// ═══════════════════════════════════════════
const QUIZ=[
  // ── Türk Savunma Sanayii ──
  {q:'Türkiye\'nin milli muharebe uçağı KAAN\'ı geliştiren kuruluş hangisidir?',opts:['ASELSAN','ROKETSAN','TAI (TUSAŞ)','HAVELSAN'],ans:2,r:600},
  {q:'Bayraktar TB2 ilk operasyonel uçuşunu hangi yıl yaptı?',opts:['2012','2014','2016','2019'],ans:2,r:500},
  {q:'ALTAY tankının seri üretim sözleşmesi hangi şirkete verildi?',opts:['FNSS','Otokar','BMC','ASELSAN'],ans:2,r:600},
  {q:'MPT-76 piyade tüfeği hangi kalibreyi kullanır?',opts:['5.56mm NATO','7.62mm NATO','9mm','12.7mm'],ans:1,r:600},
  {q:'Türkiye\'nin yerli ve milli insansız deniz aracı hangisidir?',opts:['ULAQ','ALBATROS','DENİZHAN','MARLIN'],ans:0,r:600},
  {q:'Türkiye\'nin ilk yerli ve milli muharip insansız hava aracı hangisidir?',opts:['Bayraktar TB2','Akıncı','ANKA-S','Kızılelma'],ans:3,r:700},
  {q:'ASELSAN\'ın genel merkezi hangi şehirdedir?',opts:['İstanbul','İzmir','Ankara','Bursa'],ans:2,r:400},
  {q:'Türkiye\'nin milli roketatar sistemi hangisidir?',opts:['T-122 Sakarya','J-600T Yıldırım','TRLG-230','Hepsi'],ans:3,r:600},
  {q:'HİSAR hava savunma sistemini geliştiren firmalar hangileridir?',opts:['ASELSAN-ROKETSAN','TAI-HAVELSAN','FNSS-BMC','STM-METEKSAN'],ans:0,r:600},
  {q:'Türkiye\'nin denizaltıları hangi sınıfa aittir?',opts:['Los Angeles','Preveze (Type 209)','Kilo','Soryu'],ans:1,r:700},
  {q:'TCG Anadolu hangi tür bir gemidir?',opts:['Uçak gemisi','İnsansız sistemler gemisi (LHD)','Fırkateyn','Denizaltı'],ans:1,r:700},
  {q:'ROKETSAN\'ın ürettiği Cirit roketi ne tür bir silahtır?',opts:['Denizden karaya füze','Lazer güdümlü roket','Uçaksavar füzesi','Torpido'],ans:1,r:600},
  {q:'Türkiye\'nin SİHA\'larında kullanılan MAM-L akıllı mikro mühimmat hangi firmaya aittir?',opts:['ASELSAN','STM','ROKETSAN','TAI'],ans:2,r:600},
  {q:'Bayraktar Kızılelma\'nın motorunu hangi firma üretmektedir?',opts:['TUSAŞ','TEI','ROKETSAN','İvchenko-Progress'],ans:3,r:700},
  {q:'Türk Silahlı Kuvvetleri\'nin ana muharebe tankı hangisidir?',opts:['M60T Sabra','Leopard 2A4','M48T5','ALTAY'],ans:1,r:500},
  // ── Türk Tarihi & Savaşlar ──
  {q:'Kurtuluş Savaşı\'nda Büyük Taarruz hangi yıl gerçekleşti?',opts:['1919','1920','1921','1922'],ans:3,r:500},
  {q:'Çanakkale Savaşı hangi yıllar arasındaydı?',opts:['1912-1913','1914-1915','1915-1916','1916-1917'],ans:2,r:500},
  {q:'Sakarya Meydan Muharebesi hangi yılda kazanıldı?',opts:['1919','1920','1921','1922'],ans:2,r:500},
  {q:'Türkiye Cumhuriyeti hangi tarihte kuruldu?',opts:['23 Nisan 1920','30 Ağustos 1922','29 Ekim 1923','10 Kasım 1938'],ans:2,r:400},
  {q:'İnönü Muharebelerini kazanan komutan kimdir?',opts:['Mustafa Kemal','İsmet İnönü','Fevzi Çakmak','Kazım Karabekir'],ans:1,r:500},
  {q:'Türkiye NATO\'ya hangi yıl katıldı?',opts:['1949','1952','1955','1961'],ans:1,r:600},
  {q:'Kıbrıs Barış Harekâtı hangi yıl gerçekleşti?',opts:['1964','1967','1974','1980'],ans:2,r:500},
  {q:'Türk Silahlı Kuvvetleri\'nin kuruluş tarihi hangi savaşa dayanır?',opts:['Kurtuluş Savaşı','Balkan Savaşları','Çanakkale','Osmanlı-Rus Savaşı'],ans:0,r:600},
  {q:'30 Ağustos Zafer Bayramı hangi zaferi kutlar?',opts:['Sakarya Muharebesi','Büyük Taarruz','İnönü Muharebesi','Dumlupınar Muharebesi'],ans:1,r:500},
  {q:'Türkiye\'nin Kore Savaşı\'na gönderdiği asker sayısı yaklaşık kaçtır?',opts:['1.000','5.000','15.000','25.000'],ans:2,r:700},
  // ── NATO & Uluslararası ──
  {q:'NATO\'ya en son katılan ülke hangisidir?',opts:['Finlandiya','İsveç','Karadağ','Kuzey Makedonya'],ans:1,r:600},
  {q:'NATO\'nun kuruluş yılı hangisidir?',opts:['1945','1947','1949','1951'],ans:2,r:500},
  {q:'NATO\'nun genel merkezi hangi şehirdedir?',opts:['Londra','Paris','Brüksel','Amsterdam'],ans:2,r:400},
  {q:'NATO Madde 5 neyi ifade eder?',opts:['Ekonomik yaptırım','Kolektif savunma','Silah kontrolü','Barış gücü'],ans:1,r:600},
  {q:'NATO\'nun kaç üye ülkesi vardır? (2024)',opts:['28','30','32','35'],ans:2,r:600},
  {q:'Dünya\'nın en büyük askeri harcamasını yapan ülke hangisidir?',opts:['Çin','Rusya','ABD','Hindistan'],ans:2,r:400},
  // ── Dünya Savaşları ──
  {q:'Normandiya çıkarması (D-Day) hangi tarihte oldu?',opts:['6 Haziran 1944','8 Mayıs 1945','1 Eylül 1939','6 Ağustos 1945'],ans:0,r:500},
  {q:'II. Dünya Savaşı hangi tarihte sona erdi?',opts:['6 Ağustos 1945','2 Eylül 1945','8 Mayıs 1945','1 Eylül 1945'],ans:1,r:600},
  {q:'I. Dünya Savaşı\'nı sona erdiren antlaşma hangisidir?',opts:['Versailles','Lozan','Sevr','Paris'],ans:0,r:600},
  {q:'Stalingrad Muharebesi hangi yıllarda yaşandı?',opts:['1940-41','1941-42','1942-43','1943-44'],ans:2,r:600},
  {q:'Hiroşima\'ya atom bombası hangi tarihte atıldı?',opts:['6 Ağustos 1945','9 Ağustos 1945','2 Eylül 1945','8 Mayıs 1945'],ans:0,r:500},
  {q:'II. Dünya Savaşı\'nda Almanya\'nın Sovyetler\'e saldırı operasyonunun adı nedir?',opts:['Operasyon Sealion','Operasyon Barbarossa','Operasyon Overlord','Operasyon Market Garden'],ans:1,r:600},
  {q:'Blitzkrieg taktiği hangi ülke tarafından geliştirilmiştir?',opts:['İngiltere','ABD','Almanya','Fransa'],ans:2,r:500},
  {q:'Pearl Harbor saldırısı hangi tarihte gerçekleşti?',opts:['7 Aralık 1941','6 Haziran 1944','8 Mayıs 1945','1 Eylül 1939'],ans:0,r:500},
  // ── Hava Kuvvetleri & Uçaklar ──
  {q:'F-16 savaş uçağının üreticisi hangi şirkettir?',opts:['Boeing','Lockheed Martin','Northrop Grumman','Raytheon'],ans:1,r:500},
  {q:'Stealth teknolojisini kullanan ilk üretim uçağı hangisidir?',opts:['F-22 Raptor','B-2 Spirit','F-117 Nighthawk','SR-71'],ans:2,r:700},
  {q:'F-35 uçağını geliştiren program hangisidir?',opts:['JSF (Joint Strike Fighter)','ATF','FX','NGAD'],ans:0,r:600},
  {q:'Dünyanın en hızlı insanlı uçağı hangisidir?',opts:['F-22','SR-71 Blackbird','MiG-25','X-15'],ans:1,r:700},
  {q:'Su-57 hangi ülkenin 5. nesil savaş uçağıdır?',opts:['Çin','Rusya','Hindistan','İran'],ans:1,r:500},
  {q:'B-2 Spirit bombardıman uçağının kanat açıklığı kaç metredir?',opts:['42m','52m','62m','72m'],ans:1,r:700},
  {q:'Apache saldırı helikopterinin üreticisi hangisidir?',opts:['Sikorsky','Boeing','Bell','Airbus'],ans:1,r:500},
  {q:'Dünyanın en büyük kargo uçağı hangisidir?',opts:['C-17','An-124','An-225 Mriya','C-5 Galaxy'],ans:2,r:600},
  {q:'AWACS uçaklarının görevi nedir?',opts:['Bombalama','Hava gözetleme ve komuta','İkmal','Keşif fotoğrafçılığı'],ans:1,r:600},
  {q:'Türk Hava Kuvvetleri\'nin kullandığı eğitim uçağı hangisidir?',opts:['T-38 Talon','KT-1 Woongbi','SF-260','Hürjet'],ans:0,r:600},
  // ── Kara Kuvvetleri & Tanklar ──
  {q:'Dünyanın en fazla üretilen tankı hangisidir?',opts:['M4 Sherman','T-34','T-54/55','Leopard 2'],ans:2,r:600},
  {q:'M1 Abrams tankının motorunun türü nedir?',opts:['Dizel','Gaz türbini','Elektrik','Benzin'],ans:1,r:700},
  {q:'Leopard 2A7 tankı hangi ülke tarafından üretilmektedir?',opts:['İsviçre','Almanya','Avusturya','Hollanda'],ans:1,r:500},
  {q:'Challenger 2 hangi ülkenin ana muharebe tankıdır?',opts:['Fransa','İngiltere','Avustralya','Kanada'],ans:1,r:500},
  {q:'Dünya\'nın en ağır tankı olarak bilinen Maus\'u hangi ülke yaptı?',opts:['SSCB','İngiltere','ABD','Almanya'],ans:3,r:600},
  {q:'AK-47\'nin tasarımcısı kimdir?',opts:['Stechkin','Dragunov','Kalashnikov','Simonov'],ans:2,r:400},
  {q:'NATO standardı piyade tüfeği kalibresi nedir?',opts:['7.62mm','5.56mm','9mm','6.5mm Creedmoor'],ans:1,r:500},
  {q:'RPG-7 hangi tür bir silahtır?',opts:['Makineli tüfek','El bombası fırlatıcı','Taşınabilir roketatar','Keskin nişancı tüfeği'],ans:2,r:500},
  {q:'Bradley piyade savaş aracı hangi ülke tarafından kullanılır?',opts:['Almanya','Fransa','ABD','İngiltere'],ans:2,r:500},
  {q:'MANPADS kısaltması neyi ifade eder?',opts:['Man-Portable Air Defense System','Multiple Artillery Delivery System','Mounted Anti-Personnel Defense','Mobile Air Navigation System'],ans:0,r:700},
  // ── Deniz Kuvvetleri ──
  {q:'Dünyanın en büyük uçak gemisi hangi ülkeye aittir?',opts:['Çin','Rusya','Japonya','ABD'],ans:3,r:400},
  {q:'Denizaltıların ses altı hızla ilerleme yeteneğine ne denir?',opts:['Stealth','AIP (Air Independent Propulsion)','Sonar','SLBM'],ans:1,r:700},
  {q:'Türk Deniz Kuvvetleri\'nin amiral gemisi hangisidir?',opts:['TCG Anadolu','TCG Kemalreis','TCG Barbaros','TCG Yıldırım'],ans:0,r:600},
  {q:'İlk nükleer denizaltı hangisidir?',opts:['USS Nautilus','HMS Dreadnought','K-19','USS George Washington'],ans:0,r:600},
  {q:'Exocet füzesi hangi ülke tarafından üretilmektedir?',opts:['İngiltere','Almanya','Fransa','İtalya'],ans:2,r:600},
  // ── Füzeler & Hava Savunma ──
  {q:'S-400 hava savunma sistemi hangi ülkenin üretimidir?',opts:['Çin','ABD','Rusya','Fransa'],ans:2,r:500},
  {q:'Patriot hava savunma sistemini hangi ülke üretmektedir?',opts:['İsrail','Almanya','ABD','İngiltere'],ans:2,r:500},
  {q:'SHORAD kısaltması neyi ifade eder?',opts:['Short Range Air Defense','Super High Radar','Standoff Range Denial','Ship Radar Array'],ans:0,r:700},
  {q:'Tomahawk hangi tür bir silahtır?',opts:['Balistik füze','Seyir füzesi','Hava-hava füzesi','Roket'],ans:1,r:500},
  {q:'ICBM kısaltması ne anlama gelir?',opts:['Intercontinental Ballistic Missile','Infantry Combat Ballistic Mortar','International Combat Battle Machine','Integrated Command and Battle Module'],ans:0,r:600},
  {q:'Iron Dome hangi ülke tarafından geliştirilmiştir?',opts:['ABD','Türkiye','İsrail','Güney Kore'],ans:2,r:500},
  {q:'Türkiye\'nin SİPER uzun menzilli hava savunma sistemini kimler geliştiriyor?',opts:['Sadece ASELSAN','ASELSAN ve ROKETSAN','TAI ve HAVELSAN','STM ve METEKSAN'],ans:1,r:700},
  {q:'Hypersonic (hipersonik) silah Mach kaçın üzerinde hızda gider?',opts:['Mach 3','Mach 5','Mach 8','Mach 10'],ans:1,r:600},
  // ── Teknoloji & Siber ──
  {q:'İnsansız kara aracı RIPSAW hangi ülkenin ürünüdür?',opts:['Türkiye','İsrail','ABD','Almanya'],ans:2,r:600},
  {q:'EMP silahı ne işe yarar?',opts:['Elektromanyetik nabız ile elektronik sistemleri devre dışı bırakır','Kimyasal madde yayar','Lazer ile hedef yakar','Ses dalgaları ile saldırır'],ans:0,r:700},
  {q:'Dünyanın ilk "drone savaşı" olarak kabul edilen çatışma hangisidir?',opts:['Suriye İç Savaşı','Dağlık Karabağ Savaşı','Yemen İç Savaşı','Libya Çatışması'],ans:1,r:700},
  {q:'STM\'nin geliştirdiği yapay zeka destekli drone sürüsü projesinin adı nedir?',opts:['KARGU','ALPAGU','BOYGA','TOGAN'],ans:0,r:700},
  {q:'Türkiye\'nin ilk silahlı insansız denizaltısı hangisidir?',opts:['MARLIN','ARES','ULAQ','ALBATROS'],ans:0,r:700},
  // ── Genel Askeri Bilgi ──
  {q:'Bir tugayda kaç alay bulunur?',opts:['2','3','4','5'],ans:1,r:600},
  {q:'"Müşterek harekât" terimi neyi ifade eder?',opts:['Tek kuvvet harekâtı','Birden fazla kuvvetin koordineli harekâtı','İstihbarat operasyonu','Deniz çıkarması'],ans:1,r:600},
  {q:'Meşhur "Savaş sanatı" kitabının yazarı kimdir?',opts:['Julius Caesar','Carl von Clausewitz','Sun Tzu','Napoleon'],ans:2,r:500},
  {q:'NATO\'da kullanılan "STANAG" kısaltması neyi ifade eder?',opts:['Standard Agreement','Strategic Action Guide','Support and Aid Group','Surveillance Technology'],ans:0,r:700},
  {q:'Askeri rütbe sırasında "tuğgeneral" hangi rütbenin altındadır?',opts:['Korgeneral','Tümgeneral','General','Orgeneral'],ans:1,r:500},
  {q:'Türk Jandarması hangi bakanlığa bağlıdır?',opts:['Milli Savunma Bakanlığı','İçişleri Bakanlığı','Adalet Bakanlığı','Her ikisine de'],ans:3,r:600},
  {q:'Türkiye\'nin en büyük askeri tatbikatı hangisidir?',opts:['Efes Tatbikatı','Deniz Kurdu','Kartal Kalkanı','Yıldırım'],ans:0,r:600},
  {q:'GPS sistemi ilk olarak hangi amaçla geliştirildi?',opts:['Sivil navigasyon','Askeri konumlandırma','Hava trafiği','Meteoroloji'],ans:1,r:500},
  {q:'Dünyada aktif nükleer silahlara sahip ülke sayısı kaçtır?',opts:['5','7','9','11'],ans:2,r:600},
  {q:'Türkiye Kore Savaşı\'nda hangi cephe safında savaştı?',opts:['Komünist Kuzey','BM Güney Koaliyonu','Tarafsız','Çin'],ans:1,r:500},
];

// ═══════════════════════════════════════════
//  UNIT REGISTRY — 50 PERFECTLY MIRRORED PAIRS
//  id,nm,s(p/e),a(0-4),c(cost),h(hp),d(dmg),sp(speed),
//  r(range),rt(attack rate ms),rw(reward),xp,t(type),air(0/1),km(kamikaze)
// ═══════════════════════════════════════════
const REG=[
// ── AGE 0 ── MIRRORED PAIRS
{id:'p_g3',   nm:'G3 Eri',          s:'p',a:0,c:100, h:78,  d:17, sp:59,r:205,rt:1950,rw:50, xp:30,t:'inf',  air:0},
{id:'e_ak47', nm:'AK-47 Militan',   s:'e',a:0,c:100, h:78,  d:17, sp:59,r:205,rt:1950,rw:50, xp:30,t:'inf',  air:0},
{id:'p_m60mg',nm:'M60 Makineli',    s:'p',a:0,c:200, h:120, d:25, sp:46,r:210,rt:1400,rw:100,xp:60,t:'heavy',air:0},
{id:'e_rpg1', nm:'RPG Operatörü',   s:'e',a:0,c:200, h:120, d:50, sp:46,r:210,rt:2800,rw:100,xp:60,t:'inf',  air:0},
{id:'p_m48',  nm:'M48 Tank',        s:'p',a:0,c:450, h:435, d:70, sp:37,r:258,rt:3350,rw:225,xp:140,t:'veh', air:0},
{id:'e_t55',  nm:'T-55 Tank',       s:'e',a:0,c:450, h:435, d:70, sp:37,r:258,rt:3350,rw:225,xp:140,t:'veh', air:0},
{id:'p_m113', nm:'M113 APC',        s:'p',a:0,c:280, h:270, d:33, sp:53,r:210,rt:2200,rw:140,xp:85,t:'veh',  air:0},
{id:'e_bmp1', nm:'BMP-1',           s:'e',a:0,c:280, h:270, d:33, sp:53,r:210,rt:2200,rw:140,xp:85,t:'veh',  air:0},
{id:'p_f4',   nm:'F-4 Phantom',     s:'p',a:0,c:700, h:190, d:52, sp:157,r:235,rt:2350,rw:350,xp:220,t:'air',air:1},
{id:'e_mig21',nm:'MiG-21',          s:'e',a:0,c:700, h:190, d:52, sp:157,r:235,rt:2350,rw:350,xp:220,t:'air',air:1},
// ── AGE 1 ── MIRRORED PAIRS
{id:'p_hk33', nm:'HK33 Eri',        s:'p',a:1,c:150, h:98,  d:21, sp:61,r:220,rt:1750,rw:75, xp:45,t:'inf',  air:0},
{id:'e_ak74', nm:'AK-74 Militan',   s:'e',a:1,c:150, h:98,  d:21, sp:61,r:220,rt:1750,rw:75, xp:45,t:'inf',  air:0},
{id:'p_snp',  nm:'Keskin Nişancı',  s:'p',a:1,c:280, h:88,  d:60, sp:43,r:295,rt:3000,rw:140,xp:85,t:'inf',  air:0},
{id:'e_rpg7', nm:'RPG-7',           s:'e',a:1,c:280, h:88,  d:60, sp:43,r:295,rt:3000,rw:140,xp:85,t:'inf',  air:0},
{id:'p_leo1', nm:'Leopard 1T',      s:'p',a:1,c:600, h:590, d:88, sp:41,r:280,rt:2900,rw:300,xp:180,t:'veh', air:0},
{id:'e_t72',  nm:'T-72 Tank',       s:'e',a:1,c:600, h:590, d:88, sp:41,r:280,rt:2900,rw:300,xp:180,t:'veh', air:0},
{id:'p_kirpi',nm:'Kirpi MRAP',      s:'p',a:1,c:350, h:390, d:39, sp:59,r:220,rt:2000,rw:175,xp:105,t:'veh', air:0},
{id:'e_btr80',nm:'BTR-80',          s:'e',a:1,c:350, h:390, d:39, sp:59,r:220,rt:2000,rw:175,xp:105,t:'veh', air:0},
{id:'p_ah1',  nm:'AH-1W Kobra',     s:'p',a:1,c:800, h:350, d:65, sp:85,r:240,rt:1650,rw:400,xp:250,t:'air', air:1},
{id:'e_mi24', nm:'Mi-24 Hind',      s:'e',a:1,c:800, h:350, d:65, sp:85,r:240,rt:1650,rw:400,xp:250,t:'air', air:1},
// ── AGE 2 ── MIRRORED PAIRS
{id:'p_mpt',  nm:'MPT-76 Eri',      s:'p',a:2,c:200, h:125, d:27, sp:61,r:235,rt:1400,rw:100,xp:60,t:'inf',  air:0},
{id:'e_pkm',  nm:'PKM Makineli',    s:'e',a:2,c:200, h:125, d:27, sp:61,r:235,rt:1400,rw:100,xp:60,t:'heavy',air:0},
{id:'p_manp', nm:'MANPADS',         s:'p',a:2,c:380, h:95,  d:90, sp:45,r:305,rt:4050,rw:190,xp:115,t:'inf', air:0},
{id:'e_atgm', nm:'ATGM Operatörü',  s:'e',a:2,c:380, h:95,  d:90, sp:45,r:305,rt:4050,rw:190,xp:115,t:'inf', air:0},
{id:'p_leo2', nm:'Leopard 2A4',     s:'p',a:2,c:850, h:785, d:112,sp:44,r:297,rt:2700,rw:425,xp:255,t:'veh', air:0},
{id:'e_t90',  nm:'T-90 Tank',       s:'e',a:2,c:850, h:785, d:112,sp:44,r:297,rt:2700,rw:425,xp:255,t:'veh', air:0},
{id:'p_acv',  nm:'ACV-15 İFV',      s:'p',a:2,c:500, h:505, d:55, sp:61,r:240,rt:1750,rw:250,xp:150,t:'veh', air:0},
{id:'e_bmp3', nm:'BMP-3',           s:'e',a:2,c:500, h:505, d:55, sp:61,r:240,rt:1750,rw:250,xp:150,t:'veh', air:0},
{id:'p_f16c', nm:'F-16 Block 50',   s:'p',a:2,c:1100,h:242, d:82, sp:182,r:248,rt:2000,rw:550,xp:340,t:'air',air:1},
{id:'e_mig29',nm:'MiG-29',          s:'e',a:2,c:1100,h:242, d:82, sp:182,r:248,rt:2000,rw:550,xp:340,t:'air',air:1},
// ── AGE 3 ── MIRRORED PAIRS
{id:'p_sar',  nm:'SAR 56 Eri',      s:'p',a:3,c:250, h:160, d:33, sp:65,r:250,rt:1450,rw:125,xp:75,t:'inf',  air:0},
{id:'e_mod',  nm:'Modern Militan',  s:'e',a:3,c:250, h:160, d:33, sp:65,r:250,rt:1450,rw:125,xp:75,t:'inf',  air:0},
{id:'p_jso',  nm:'JÖH Operatörü',   s:'p',a:3,c:450, h:150, d:52, sp:70,r:235,rt:1100,rw:225,xp:135,t:'inf', air:0},
{id:'e_fpv',  nm:'FPV Drone',       s:'e',a:3,c:450, h:150, d:55, sp:130,r:0,rt:0,    rw:225,xp:135,t:'air', air:1,km:1},
{id:'p_altay',nm:'ALTAY Tank',      s:'p',a:3,c:1000,h:980, d:140,sp:46,r:310,rt:2500,rw:500,xp:300,t:'veh', air:0},
{id:'e_t90ms',nm:'T-90MS',          s:'e',a:3,c:1000,h:980, d:140,sp:46,r:310,rt:2500,rw:500,xp:300,t:'veh', air:0},
{id:'p_pars', nm:'PARS İFV',        s:'p',a:3,c:650, h:620, d:98, sp:51,r:295,rt:2850,rw:325,xp:195,t:'veh', air:0},
{id:'e_tos1', nm:'TOS-1',           s:'e',a:3,c:650, h:620, d:98, sp:51,r:295,rt:2850,rw:325,xp:195,t:'veh', air:0},
{id:'p_tb2',  nm:'Bayraktar TB2',   s:'p',a:3,c:900, h:210, d:72, sp:100,r:248,rt:2900,rw:450,xp:270,t:'air',air:1},
{id:'e_su35', nm:'Su-35',           s:'e',a:3,c:900, h:210, d:72, sp:190,r:248,rt:1900,rw:450,xp:270,t:'air',air:1},
{id:'p_f35',  nm:'F-35A',           s:'p',a:3,c:1400,h:275, d:95, sp:192,r:252,rt:1750,rw:700,xp:430,t:'air',air:1},
{id:'e_ka52', nm:'Ka-52 Alligator', s:'e',a:3,c:1400,h:410, d:95, sp:91, r:240,rt:1500,rw:700,xp:430,t:'air',air:1},
// ── AGE 4 ── MIRRORED PAIRS
{id:'p_exo',  nm:'Exo-Suit Askeri', s:'p',a:4,c:350, h:230, d:46, sp:72,r:275,rt:1100,rw:175,xp:105,t:'inf', air:0},
{id:'e_exoe', nm:'Düşman Exo-Suit', s:'e',a:4,c:350, h:230, d:46, sp:72,r:275,rt:1100,rw:175,xp:105,t:'inf', air:0},
{id:'p_cyber',nm:'Siber Komando',   s:'p',a:4,c:550, h:190, d:68, sp:68,r:305,rt:950, rw:275,xp:165,t:'inf', air:0},
{id:'e_laser',nm:'Lazer Operatörü', s:'e',a:4,c:550, h:190, d:68, sp:68,r:305,rt:950, rw:275,xp:165,t:'inf', air:0},
{id:'p_altx', nm:'ALTAY-X',         s:'p',a:4,c:1500,h:1460,d:200,sp:50,r:355,rt:2200,rw:750,xp:450,t:'veh', air:0},
{id:'e_futank',nm:'Gelecek Tankı',  s:'e',a:4,c:1500,h:1460,d:200,sp:50,r:355,rt:2200,rw:750,xp:450,t:'veh', air:0},
{id:'p_anka3',nm:'ANKA-3 UCAV',     s:'p',a:4,c:1200,h:212, d:85, sp:140,r:252,rt:2600,rw:600,xp:360,t:'air',air:1},
{id:'e_stlth',nm:'Stealth Drone',   s:'e',a:4,c:1200,h:212, d:85, sp:135,r:252,rt:2800,rw:600,xp:360,t:'air',air:1},
{id:'p_kzelma',nm:'Kızılelma',      s:'p',a:4,c:2000,h:320, d:115,sp:212,r:258,rt:1500,rw:1000,xp:600,t:'air',air:1},
{id:'e_hypers',nm:'Hipersonik Füze',s:'e',a:4,c:2000,h:110, d:480,sp:260,r:0,rt:0,    rw:1000,xp:600,t:'air',air:1,km:1},
// ── KESKİN NİŞANCI (her çağ, sadece oyuncu — uzun menzil, yüksek hasar, yavaş ateş)
{id:'p_snp0',nm:'🎯 Keskin Nişancı',  s:'p',a:0,c:450, h:90, d:95, sp:52,r:370,rt:3800,rw:230,xp:135,t:'inf',air:0,special:1},
{id:'p_snp1',nm:'🎯 Nişancı (2000)',  s:'p',a:1,c:580, h:115,d:130,sp:54,r:390,rt:3600,rw:290,xp:175,t:'inf',air:0,special:1},
{id:'p_snp2',nm:'🎯 Nişancı (2010)',  s:'p',a:2,c:720, h:145,d:175,sp:55,r:415,rt:3300,rw:360,xp:215,t:'inf',air:0,special:1},
{id:'p_snp3',nm:'🎯 JÖH Nişancı',    s:'p',a:3,c:900, h:180,d:230,sp:57,r:440,rt:3100,rw:450,xp:270,t:'inf',air:0,special:1},
{id:'p_snp4',nm:'🎯 Siber Nişancı',  s:'p',a:4,c:1100,h:220,d:300,sp:59,r:465,rt:2900,rw:550,xp:330,t:'inf',air:0,special:1},
// ── MEDİKAL APC (her çağ, sadece oyuncu — savaş alanında iyileştirme)
{id:'p_med0',nm:'🚑 Sıhhiye Aracı',  s:'p',a:0,c:500, h:300,d:12, sp:50,r:140,rt:4000,rw:100,xp:60,t:'veh',air:0,special:1,healer:1},
{id:'p_med1',nm:'🚑 M113 Sıhhiye',   s:'p',a:1,c:650, h:400,d:15, sp:52,r:155,rt:3800,rw:130,xp:78,t:'veh',air:0,special:1,healer:1},
{id:'p_med2',nm:'🚑 Kirpi Sıhhiye',  s:'p',a:2,c:820, h:520,d:18, sp:54,r:165,rt:3600,rw:160,xp:96,t:'veh',air:0,special:1,healer:1},
{id:'p_med3',nm:'🚑 ARMA Sıhhiye',   s:'p',a:3,c:1000,h:660,d:22, sp:56,r:175,rt:3400,rw:200,xp:120,t:'veh',air:0,special:1,healer:1},
{id:'p_med4',nm:'🚑 Nano Sıhhiye',   s:'p',a:4,c:1250,h:820,d:28, sp:58,r:185,rt:3200,rw:250,xp:150,t:'veh',air:0,special:1,healer:1},
// ── KOMANDO BİRİMLERİ (her çağ için 1 özel birim, sadece oyuncu)
{id:'p_kmd0',nm:'🎖 Komando (Soğuk Savaş)',s:'p',a:0,c:600, h:340,d:48, sp:65,r:240,rt:1200,rw:300,xp:180,t:'heavy',air:0,special:1},
{id:'p_kmd1',nm:'🎖 Özel Kuvvet (2000)',   s:'p',a:1,c:800, h:430,d:66, sp:68,r:255,rt:1100,rw:400,xp:240,t:'heavy',air:0,special:1},
{id:'p_kmd2',nm:'🎖 Özel Tim (2010)',       s:'p',a:2,c:1050,h:560,d:88, sp:70,r:270,rt:1000,rw:525,xp:315,t:'heavy',air:0,special:1},
{id:'p_kmd3',nm:'🎖 JÖH Timi (2020)',       s:'p',a:3,c:1300,h:700,d:112,sp:72,r:285,rt:950, rw:650,xp:390,t:'heavy',air:0,special:1},
{id:'p_kmd4',nm:'🎖 Siber Komando (2030)',  s:'p',a:4,c:1700,h:900,d:145,sp:76,r:305,rt:900, rw:850,xp:510,t:'heavy',air:0,special:1},
];

// ═══════════════════════════════════════════
//  VISUAL PARAMS — unique drawing per unit
// ═══════════════════════════════════════════
const VP={
p_g3:   {fn:'inf', bc:0x3d5c2e,hc:0x2c4220,hst:0,wk:'g3',  vs:0x4a5e3a,sk:0xc89060},
p_m60mg:{fn:'inf', bc:0x3d5c2e,hc:0x2c4220,hst:1,wk:'mg',  vs:0x556622,sk:0xc89060},
p_m48:  {fn:'tank',hc:0x4a5e3a,tc:0x3d5229,blen:22,hw:40,hh:15,tw:18,th:10,era:0,cm:0},
p_m113: {fn:'veh', bc:0x4a5e3a,w:42,h:18,gc:0x666655,nt:'mg'},
p_f4:   {fn:'jet', bc:0x7a8090,ac:0x556070,blen:46,bh:8, ws:36,wa:35,twin:1,stl:0},
e_ak47: {fn:'inf', bc:0x8a7050,hc:0x5c3c1e,hst:2,wk:'ak47',vs:0,sk:0xb07850},
e_rpg1: {fn:'inf', bc:0x8a7050,hc:0x5c3c1e,hst:2,wk:'rpg', vs:0,sk:0xb07850},
e_t55:  {fn:'tank',hc:0x3c4a30,tc:0x2e3c24,blen:19,hw:38,hh:14,tw:16,th:9, era:0,cm:0},
e_bmp1: {fn:'veh', bc:0x3c4a30,w:38,h:15,gc:0x445533,nt:'gun'},
e_mig21:{fn:'jet', bc:0x5a6070,ac:0x3a4050,blen:40,bh:6, ws:24,wa:55,twin:0,stl:0},
p_hk33: {fn:'inf', bc:0x3d5c2e,hc:0x556622,hst:1,wk:'hk33',vs:0x4a6633,sk:0xc89060},
p_snp:  {fn:'inf', bc:0x3a4e28,hc:0x3a4220,hst:0,wk:'snp', vs:0,sk:0xc89060},
p_leo1: {fn:'tank',hc:0x4a5e3a,tc:0x4a6022,blen:28,hw:44,hh:16,tw:20,th:11,era:0,cm:1},
p_kirpi:{fn:'veh', bc:0x556644,w:46,h:20,gc:0x778855,nt:'mg'},
p_ah1:  {fn:'heli',bc:0x4a6044,slim:1,pods:1,tc:0x3a5033},
e_ak74: {fn:'inf', bc:0x6a5838,hc:0x4c3820,hst:3,wk:'ak74',vs:0,sk:0xa87040},
e_rpg7: {fn:'inf', bc:0x6a5838,hc:0x4c3820,hst:3,wk:'rpg7',vs:0,sk:0xa87040},
e_t72:  {fn:'tank',hc:0x2c3c24,tc:0x243018,blen:25,hw:42,hh:15,tw:20,th:10,era:0,cm:0},
e_btr80:{fn:'veh', bc:0x3c4830,w:44,h:16,gc:0x446040,nt:'gun',wheels:1},
e_mi24: {fn:'heli',bc:0x3c4a30,slim:0,pods:1,tc:0x2c3820},
p_mpt:  {fn:'inf', bc:0x3a5a30,hc:0x556622,hst:4,wk:'mpt', vs:0x4a6633,sk:0xc07850},
p_manp: {fn:'inf', bc:0x3a5a30,hc:0x556622,hst:4,wk:'manp',vs:0x4a6633,sk:0xc07850},
p_leo2: {fn:'tank',hc:0x485e38,tc:0x2c3c1e,blen:32,hw:46,hh:17,tw:22,th:12,era:0,cm:2},
p_acv:  {fn:'veh', bc:0x4a5e3a,w:48,h:19,gc:0x667744,nt:'cannon'},
p_f16c: {fn:'jet', bc:0x8090a0,ac:0x607080,blen:42,bh:7, ws:28,wa:25,twin:0,stl:0},
e_pkm:  {fn:'inf', bc:0x4a4030,hc:0x3a3020,hst:3,wk:'mg',  vs:0,sk:0xa07040},
e_atgm: {fn:'inf', bc:0x4a4030,hc:0x3a3020,hst:3,wk:'atgm',vs:0,sk:0xa07040},
e_t90:  {fn:'tank',hc:0x2c3020,tc:0x242818,blen:28,hw:44,hh:16,tw:21,th:11,era:1,cm:0},
e_bmp3: {fn:'veh', bc:0x303828,w:42,h:17,gc:0x445535,nt:'cannon'},
e_mig29:{fn:'jet', bc:0x5a6050,ac:0x404830,blen:44,bh:8, ws:32,wa:30,twin:1,stl:0},
p_sar:  {fn:'inf', bc:0x3a5a30,hc:0x2c4420,hst:4,wk:'sar', vs:0x5a7044,sk:0xc07850},
p_jso:  {fn:'inf', bc:0x222a20,hc:0x1a2018,hst:4,wk:'sar', vs:0x2c3828,sk:0xb07040},
p_altay:{fn:'tank',hc:0x5a6644,tc:0x485840,blen:34,hw:48,hh:18,tw:22,th:12,era:1,cm:3},
p_pars: {fn:'veh', bc:0x4a5a3c,w:50,h:20,gc:0x5a6a48,nt:'cannon'},
p_tb2:  {fn:'drone',bc:0x8a9090,ws:54,bh:6,pusher:1,ytail:1},
p_f35:  {fn:'jet', bc:0x6a7080,ac:0x404858,blen:44,bh:8, ws:30,wa:20,twin:0,stl:1},
e_mod:  {fn:'inf', bc:0x503820,hc:0x3a2818,hst:3,wk:'mod', vs:0,sk:0xa06838},
e_fpv:  {fn:'drone',bc:0xcc5522,ws:16,bh:4,pusher:0,ytail:0,quad:1},
e_t90ms:{fn:'tank',hc:0x282c20,tc:0x1e2018,blen:30,hw:46,hh:17,tw:22,th:12,era:1,cm:0},
e_tos1: {fn:'tank',hc:0x303828,tc:0x282c20,blen:14,hw:48,hh:19,tw:26,th:14,era:0,cm:0,rocket:1},
e_su35: {fn:'jet', bc:0x5a6060,ac:0x404848,blen:48,bh:9, ws:36,wa:28,twin:1,stl:0},
e_ka52: {fn:'heli',bc:0x3a4a30,slim:0,pods:1,tc:0x2c3820},
p_exo:  {fn:'inf', bc:0x3a4a5a,hc:0x5a6a7a,hst:5,wk:'exo', vs:0x4a5a6a,sk:0xd0c0b0},
p_cyber:{fn:'inf', bc:0x1a2830,hc:0x2a3840,hst:5,wk:'laser',vs:0x223040,sk:0xd0c0b0},
p_altx: {fn:'tank',hc:0x3a4a5a,tc:0x2a3848,blen:38,hw:50,hh:19,tw:24,th:13,era:1,cm:4},
p_anka3:{fn:'drone',bc:0x505868,ws:58,bh:7,pusher:1,ytail:1},
p_kzelma:{fn:'jet',bc:0x404858,ac:0x2a3040,blen:50,bh:10,ws:34,wa:15,twin:1,stl:1},
e_exoe: {fn:'inf', bc:0x4a3020,hc:0x5a4030,hst:5,wk:'exo', vs:0x5a4030,sk:0xd0b080},
e_laser:{fn:'inf', bc:0x201828,hc:0x302038,hst:5,wk:'laser',vs:0x201828,sk:0xd0c0b0},
e_futank:{fn:'tank',hc:0x2a2830,tc:0x1e1c24,blen:36,hw:50,hh:19,tw:26,th:14,era:1,cm:4},
e_stlth:{fn:'drone',bc:0x282830,ws:56,bh:5,pusher:0,ytail:0,stl:1},
e_hypers:{fn:'jet',bc:0x880000,ac:0x660000,blen:52,bh:7,ws:18,wa:60,twin:0,stl:0},
// Keskin Nişancı VP — ghillie suit renklerinde, uzun tüfek
p_snp0:{fn:'sniper',bc:0x3a4a20,hc:0x2a3a10,sk:0xb87840,era:0},
p_snp1:{fn:'sniper',bc:0x3a5020,hc:0x2a4018,sk:0xb87840,era:1},
p_snp2:{fn:'sniper',bc:0x2a4828,hc:0x1a3818,sk:0xb07038,era:2},
p_snp3:{fn:'sniper',bc:0x1a3828,hc:0x102818,sk:0xa06030,era:3},
p_snp4:{fn:'sniper',bc:0x101828,hc:0x081018,sk:0xc0b090,era:4},
// Medikal APC VP — kırmızı haç, beyaz/açık renkli
p_med0:{fn:'medveh',bc:0xaabbaa,gc:0xffffff,w:46,h:18,era:0},
p_med1:{fn:'medveh',bc:0xaabbaa,gc:0xffffff,w:50,h:20,era:1},
p_med2:{fn:'medveh',bc:0x99aaaa,gc:0xffffff,w:52,h:20,era:2},
p_med3:{fn:'medveh',bc:0x8899aa,gc:0xffffff,w:54,h:21,era:3},
p_med4:{fn:'medveh',bc:0x708090,gc:0xffffff,w:56,h:22,era:4},
// Komando VP — heavy inf gibi çiz
p_kmd0:{fn:'inf',bc:0x2a3a1a,hc:0x1a2a0a,hst:0,wk:'g3',  vs:0x3a5a2a,sk:0xc89060},
p_kmd1:{fn:'inf',bc:0x2a4a1a,hc:0x1a3a0a,hst:1,wk:'hk33',vs:0x3a6a2a,sk:0xc89060},
p_kmd2:{fn:'inf',bc:0x2a5a1a,hc:0x1a4a0a,hst:4,wk:'mpt', vs:0x3a6a2a,sk:0xc07850},
p_kmd3:{fn:'inf',bc:0x1a3a2a,hc:0x0a2a1a,hst:4,wk:'mpt', vs:0x2a5a3a,sk:0xc07850},
p_kmd4:{fn:'inf',bc:0x182038,hc:0x101828,hst:5,wk:'laser',vs:0x202840,sk:0xd0c0b0},
};

// ═══════════════════════════════════════════
//  SOUND SYSTEM (Web Audio API)
// ═══════════════════════════════════════════
const SFX={
  ctx:null, on:true, _lastShot:0,
  init(){
    try{this.ctx=new(window.AudioContext||window.webkitAudioContext)();}catch(e){this.ctx=null;}
  },
  resume(){if(this.ctx&&this.ctx.state==='suspended')this.ctx.resume();},
  play(type){
    if(!this.on||!this.ctx)return;
    this.resume();
    if(type==='shot'||type==='cannon'){const n=Date.now();if(n-this._lastShot<75)return;this._lastShot=n;}
    try{
      switch(type){
        case 'shot':    this._noise(2200,0.07,0.09);this._osc('sawtooth',160,0.05,0.08);break;
        case 'cannon':  this._noise(700,0.13,0.22);this._osc('sawtooth',85,0.09,0.2);break;
        case 'boom':    this._noise(500,0.22,0.38);this._osc('sawtooth',100,0.12,0.35);break;
        case 'bigboom': this._noise(250,0.55,0.75);this._osc('sawtooth',55,0.38,0.7);break;
        case 'upgrade': [440,554,660,880].forEach((f,i)=>this._osc('sine',f,0.18,0.2,this.ctx.currentTime+i*.1));break;
        case 'ult':     this._noise(300,0.6,0.85);[200,130,80].forEach((f,i)=>this._osc('sawtooth',f,0.22,0.5,this.ctx.currentTime+i*.15));break;
        case 'quiz_ok': [523,659,784].forEach((f,i)=>this._osc('sine',f,0.18,0.22,this.ctx.currentTime+i*.12));break;
        case 'quiz_fail':this._osc('sawtooth',200,0.14,0.42);this._osc('sawtooth',140,0.1,0.42);break;
        case 'repair':  [400,500,350,450].forEach((f,i)=>this._osc('square',f,0.07,0.09,this.ctx.currentTime+i*.06));break;
        case 'spawn':   this._osc('sine',440,0.07,0.14);this._osc('sine',660,0.05,0.1,this.ctx.currentTime+.05);break;
        case 'turret':  this._noise(1800,0.06,0.07);break;
      }
    }catch(e){}
  },
  _osc(type,freq,gain,dur,startT){
    if(!this.ctx)return;
    const t=startT!==undefined?startT:this.ctx.currentTime;
    const o=this.ctx.createOscillator(),g=this.ctx.createGain();
    o.connect(g);g.connect(this.ctx.destination);
    o.type=type;o.frequency.value=freq;
    g.gain.setValueAtTime(gain,t);
    g.gain.exponentialRampToValueAtTime(0.001,t+dur);
    o.start(t);o.stop(t+dur+.01);
  },
  _noise(lp,gainVal,dur){
    if(!this.ctx)return;
    const len=Math.floor(this.ctx.sampleRate*dur);
    const buf=this.ctx.createBuffer(1,len,this.ctx.sampleRate);
    const data=buf.getChannelData(0);
    for(let i=0;i<len;i++)data[i]=Math.random()*2-1;
    const src=this.ctx.createBufferSource();
    src.buffer=buf;
    const f=this.ctx.createBiquadFilter();f.type='lowpass';f.frequency.value=lp;
    const g=this.ctx.createGain();
    src.connect(f);f.connect(g);g.connect(this.ctx.destination);
    g.gain.setValueAtTime(gainVal,this.ctx.currentTime);
    g.gain.exponentialRampToValueAtTime(0.001,this.ctx.currentTime+dur);
    src.start();src.stop(this.ctx.currentTime+dur+.01);
  }
};

// ═══════════════════════════════════════════
//  PROCEDURAL DRAWING FUNCTIONS
// ═══════════════════════════════════════════
function drawWeapon(g,cx,cy,wk){
  switch(wk){
    case 'g3':
      g.fillStyle(0x2a1a0a);g.fillRect(cx-4,cy-9,-8,3);
      g.fillStyle(0x3a2510);g.fillRect(cx+7,cy-10,20,3);
      g.fillStyle(0x333);g.fillRect(cx+20,cy-10,10,2);
      g.fillStyle(0x888);g.fillRect(cx+29,cy-11,2,2);
      g.fillStyle(0x333);g.fillRect(cx+12,cy-8,4,4);break;
    case 'ak47':
      g.fillStyle(0x5a3010);g.fillRect(cx-4,cy-9,-9,3);
      g.fillStyle(0x5a3c18);g.fillRect(cx+7,cy-10,17,3);
      g.fillStyle(0x3a4c3a);g.fillRect(cx+11,cy-7,5,7);
      g.fillStyle(0x555);g.fillRect(cx+18,cy-10,9,2);
      g.fillStyle(0x888);g.fillRect(cx+26,cy-11,2,2);break;
    case 'ak74':
      g.fillStyle(0x303840);g.fillRect(cx-4,cy-9,-8,3);
      g.fillStyle(0x3c4858);g.fillRect(cx+7,cy-10,17,3);
      g.fillStyle(0x2a3050);g.fillRect(cx+11,cy-7,4,6);
      g.fillStyle(0x555);g.fillRect(cx+18,cy-10,9,2);
      g.fillStyle(0x888);g.fillRect(cx+26,cy-11,2,2);break;
    case 'hk33':case 'm4':case 'sar':case 'mpt':case 'mod':
      g.fillStyle(0x2a2a2a);g.fillRect(cx-4,cy-9,-6,3);
      g.fillStyle(0x3a3a3a);g.fillRect(cx+7,cy-10,14,3);
      g.fillStyle(0x555);g.fillRect(cx+10,cy-10,5,2);
      g.fillStyle(0x2a3540);g.fillRect(cx+12,cy-7,4,5);
      g.fillStyle(0x666);g.fillRect(cx+16,cy-10,10,2);
      g.fillStyle(0x888);g.fillRect(cx+24,cy-11,2,2);break;
    case 'mg':case 'mg2':
      g.fillStyle(0x3a3020);g.fillRect(cx-4,cy-9,-8,3);
      g.fillStyle(0x3a3030);g.fillRect(cx+7,cy-11,20,4);
      g.fillStyle(0x2a2a2a);g.fillRect(cx+11,cy-7,4,5);
      g.fillStyle(0x888);g.fillRect(cx-5,cy-7,2,5);
      g.fillStyle(0x555);g.fillRect(cx+20,cy-11,11,2);
      g.fillStyle(0x777);g.fillRect(cx+29,cy-12,2,2);break;
    case 'rpg':case 'rpg7':
      g.fillStyle(0x8a7050);g.fillRect(cx+5,cy-9,24,4);
      g.fillStyle(0x556633);g.fillRect(cx+5,cy-9,8,4);
      g.fillStyle(0xcc3300);g.fillTriangle(cx+29,cy-11,cx+35,cy-9,cx+29,cy-7);break;
    case 'manp':case 'atgm':
      g.fillStyle(0x3a5040);g.fillRect(cx+3,cy-12,26,5);
      g.fillStyle(0x5a7060);g.fillRect(cx+27,cy-13,4,4);
      g.fillStyle(0x666);g.fillRect(cx-2,cy-9,6,4);break;
    case 'snp':
      g.fillStyle(0x2a2010);g.fillRect(cx-4,cy-9,-10,3);
      g.fillStyle(0x3a3020);g.fillRect(cx+7,cy-10,24,3);
      g.fillStyle(0x555);g.fillRect(cx+13,cy-13,6,3);
      g.fillStyle(0x777);g.fillRect(cx+29,cy-10,6,2);break;
    case 'exo':
      g.fillStyle(0x1a1a2a);g.fillRect(cx-4,cy-9,-5,3);
      g.fillStyle(0x2a2a3a);g.fillRect(cx+7,cy-10,16,3);
      g.fillStyle(0x404858);g.fillRect(cx+13,cy-7,5,5);
      g.fillStyle(0x99aabb);g.fillRect(cx+18,cy-10,11,2);break;
    case 'laser':
      g.fillStyle(0x0a0a1a);g.fillRect(cx-4,cy-9,-5,3);
      g.fillStyle(0x1a2030);g.fillRect(cx+7,cy-10,22,3);
      g.fillStyle(0x00ccff);g.fillRect(cx+27,cy-10,4,2);
      g.fillStyle(0x0088cc);g.fillRect(cx+13,cy-7,4,4);break;
    default:
      g.fillStyle(0x555);g.fillRect(cx+7,cy-10,14,3);break;
  }
  g.fillStyle(0x3a3a2a);g.fillRect(cx+7,cy-12,4,8);
}

function mkInfTex(g,v,TW,TH){
  const cx=TW/2,cy=TH/2+4;
  g.fillStyle(v.bc);g.fillRect(cx-5,cy+4,4,10);g.fillRect(cx+1,cy+4,4,10);
  g.fillStyle(0x111111);g.fillRect(cx-6,cy+13,5,3);g.fillRect(cx+1,cy+13,5,3);
  g.fillStyle(v.bc);g.fillRect(cx-7,cy-10,14,15);
  if(v.vs){g.lineStyle(1,v.vs,.9);g.strokeRect(cx-6,cy-9,12,10);}
  g.fillStyle(v.bc);g.fillRect(cx-11,cy-8,4,9);
  g.fillStyle(v.sk||0xc89060);g.fillCircle(cx,cy-15,5);
  switch(v.hst){
    case 0:g.fillStyle(v.hc);g.fillEllipse(cx,cy-18,14,7);break;
    case 1:g.fillStyle(v.hc);g.fillRect(cx-7,cy-21,14,7);g.fillEllipse(cx,cy-19,14,7);break;
    case 2:g.fillStyle(v.hc);g.fillEllipse(cx,cy-16,17,8);break;
    case 3:g.fillStyle(v.hc);g.fillEllipse(cx+2,cy-19,15,6);break;
    case 4:g.fillStyle(v.hc);g.fillRect(cx-7,cy-22,14,8);g.fillStyle(0x2c3e50);g.fillRect(cx+4,cy-22,5,3);break;
    case 5:g.fillStyle(v.hc);g.fillRect(cx-8,cy-23,16,10);g.fillStyle(0x80c0ff);g.fillRect(cx-5,cy-20,10,5);break;
  }
  drawWeapon(g,cx,cy,v.wk);
}

function mkTankTex(g,v,TW,TH){
  const cx=TW/2,cy=TH/2+2;
  g.fillStyle(v.trackColor||0x2a2a2a);g.fillRect(cx-v.hw/2-3,cy+v.hh/2-2,v.hw+6,6);
  g.lineStyle(1,0x444,1);
  for(let i=0;i<v.hw+4;i+=5) g.strokeLineShape(new Phaser.Geom.Line(cx-v.hw/2-3+i,cy+v.hh/2-2,cx-v.hw/2-2+i,cy+v.hh/2+4));
  g.fillStyle(v.hc);g.fillRect(cx-v.hw/2,cy-v.hh/2,v.hw,v.hh);
  if(v.era){
    const ec=Phaser.Display.Color.IntegerToColor(v.hc);
    g.fillStyle(Phaser.Display.Color.GetColor(Math.min(255,ec.red+25),Math.min(255,ec.green+25),Math.min(255,ec.blue+25)));
    g.fillRect(cx-v.hw/2+3,cy-v.hh/2+2,6,5);g.fillRect(cx-v.hw/2+11,cy-v.hh/2+2,6,5);g.fillRect(cx-v.hw/2+20,cy-v.hh/2+2,6,5);
  }
  g.fillStyle(v.tc);const tx=cx+3;
  g.fillRect(tx-v.tw/2,cy-v.hh/2-v.th,v.tw,v.th);
  if(v.rocket){g.fillStyle(0x555);for(let i=0;i<3;i++) g.fillRect(tx+v.tw/2,cy-v.hh/2-v.th+i*4,v.blen/2,2);}
  else{g.fillStyle(0x555);g.fillRect(tx+v.tw/2,cy-v.hh/2-v.th+v.th/2-1,v.blen,v.bw||3);}
  g.lineStyle(1,0x333,1);g.strokeCircle(tx,cy-v.hh/2-v.th/2,3);
  if(v.cm===1){const c=Phaser.Display.Color.IntegerToColor(v.hc);g.fillStyle(Phaser.Display.Color.GetColor(Math.min(255,c.red+30),Math.min(255,c.green+30),Math.min(255,c.blue+15)));g.fillRect(cx-v.hw/2+v.hw*.6,cy-v.hh/2,v.hw*.2,v.hh);}
  else if(v.cm===2){g.fillStyle(0x2a1a0a);g.fillRect(cx-v.hw/2+2,cy-v.hh/2+3,5,v.hh-4);g.fillStyle(0x4a5a2a);g.fillRect(cx+4,cy-v.hh/2+2,4,v.hh-3);}
  else if(v.cm===3){const c=Phaser.Display.Color.IntegerToColor(v.hc);g.fillStyle(Phaser.Display.Color.GetColor(Math.min(255,c.red+20),Math.min(255,c.green+20),Math.min(255,c.blue+10)));for(let xi=0;xi<v.hw;xi+=4) for(let yi=0;yi<v.hh;yi+=4){if((xi+yi)%8===0) g.fillRect(cx-v.hw/2+xi,cy-v.hh/2+yi,3,3);}}
  else if(v.cm===4){const c=Phaser.Display.Color.IntegerToColor(v.hc);g.fillStyle(Phaser.Display.Color.GetColor(Math.max(0,c.red-30),Math.max(0,c.green-30),Math.max(0,c.blue-20)));g.fillTriangle(cx-v.hw/2,cy-v.hh/2,cx-v.hw/2+v.hw*.3,cy-v.hh/2,cx-v.hw/2,cy+v.hh/2);}
}

function mkVehTex(g,v,TW,TH){
  const cx=TW/2,cy=TH/2+2;
  if(v.wheels){g.fillStyle(0x2a2a2a);for(let i=0;i<4;i++) g.fillCircle(cx-v.w/2+6+i*(v.w/3.5),cy+v.h/2+3,4);}
  else{g.fillStyle(0x222);g.fillRect(cx-v.w/2-2,cy+v.h/2-2,v.w+4,5);}
  g.fillStyle(v.bc);g.fillRect(cx-v.w/2,cy-v.h/2,v.w,v.h);
  g.fillStyle(v.gc||0x556644);g.fillRect(cx-v.w/2+2,cy-v.h/2+2,v.w-4,v.h-4);
  g.fillStyle(0x5599aa);g.fillRect(cx-v.w/4,cy-v.h/2+2,8,5);
  if(v.nt==='mg'||v.nt==='gun'){g.fillStyle(0x444);g.fillRect(cx+v.w/4,cy-v.h/2-3,8,2);}
  else if(v.nt==='cannon'){g.fillStyle(0x555);g.fillRect(cx+v.w/4,cy-v.h/2-2,14,3);}
  else if(v.nt==='aa'){g.fillStyle(0x444);g.fillRect(cx+v.w/4,cy-v.h/2-5,6,2);g.fillRect(cx+v.w/4,cy-v.h/2-3,6,2);}
  else if(v.nt==='mlrs'){g.fillStyle(0x333);for(let i=0;i<4;i++) g.fillRect(cx-v.w/4,cy-v.h/2-3-i*2,v.w/2,2);}
}

function mkJetTex(g,v,TW,TH){
  const cx=TW/2,cy=TH/2;
  const bl=v.blen||44,bh=v.bh||9,ws=v.ws||30,ac=v.ac||v.bc;

  // Egzoz alevi (arka)
  g.fillStyle(0xff6600,.8);g.fillTriangle(cx-bl/2-10,cy,cx-bl/2+2,cy-3,cx-bl/2+2,cy+3);
  g.fillStyle(0xffcc00,.6);g.fillTriangle(cx-bl/2-6,cy,cx-bl/2+2,cy-2,cx-bl/2+2,cy+2);

  // Stealth: delta kanadı üçgeni
  if(v.stl){
    g.fillStyle(v.bc);
    g.fillTriangle(cx-bl/2,cy+2,cx+bl/2,cy-bh/2,cx+bl/2,cy+bh/2);
    g.fillTriangle(cx-bl/2,cy-1,cx+bl/2-4,cy-bh,cx+bl/2,cy-bh/2);
    g.fillTriangle(cx-bl/2,cy+3,cx+bl/2-4,cy+bh,cx+bl/2,cy+bh/2);
    g.fillStyle(0x80c8e0,.7);g.fillEllipse(cx-bl/6,cy,bh*1.4,bh*.65);
    return;
  }

  // Gövde — elips
  g.fillStyle(v.bc);g.fillEllipse(cx,cy,bl,bh);
  // Gövde üst renk şeridi
  g.fillStyle(ac);g.fillEllipse(cx-bl*.1,cy-1,bl*.6,bh*.5);

  // Kanatlar (sweep-back)
  g.fillStyle(ac);
  if(v.twin){
    // Çift kuyruklu (Su-35, MiG-29 tarzı)
    g.fillTriangle(cx+4,cy-bh/2,  cx-ws/2,cy-ws*.35-bh/2,  cx-8,cy-bh/2);
    g.fillTriangle(cx+4,cy+bh/2,  cx-ws/2,cy+ws*.35+bh/2,  cx-8,cy+bh/2);
    // Küçük ön kanat (canard)
    g.fillStyle(v.bc);
    g.fillTriangle(cx+bl*.28,cy-bh/2, cx+bl*.28-ws*.2,cy-ws*.12-bh/2, cx+bl*.38,cy-bh/2);
    g.fillTriangle(cx+bl*.28,cy+bh/2, cx+bl*.28-ws*.2,cy+ws*.12+bh/2, cx+bl*.38,cy+bh/2);
    // Çift kuyruk
    g.fillStyle(v.bc);
    g.fillRect(cx-bl/2+4,cy-bh*1.6,4,bh*1.1);
    g.fillRect(cx-bl/2+4,cy+bh*.5,4,bh*1.1);
    // Motor podları
    g.fillStyle(0x222222);g.fillEllipse(cx-8,cy-bh*.7,18,5);g.fillEllipse(cx-8,cy+bh*.7,18,5);
  }else{
    // Tek motorlu (MiG-21 tarzı delta kanat)
    g.fillTriangle(cx,cy-bh/2,  cx-ws/2,cy-ws*.28-bh/2,  cx-ws*.1,cy-bh/2);
    g.fillTriangle(cx,cy+bh/2,  cx-ws/2,cy+ws*.28+bh/2,  cx-ws*.1,cy+bh/2);
    // Tek kuyruk
    g.fillStyle(v.bc);g.fillRect(cx-bl/2+5,cy-bh*1.5,3,bh*3);
  }

  // Cockpit camı
  g.fillStyle(0x80c8e0,.85);g.fillEllipse(cx+bl*.22,cy,bh*1.5,bh*.75);

  // Egzoz ağzı
  g.fillStyle(0x111111);
  if(v.twin){g.fillCircle(cx-bl/2+3,cy-bh*.7,4);g.fillCircle(cx-bl/2+3,cy+bh*.7,4);}
  else{g.fillCircle(cx-bl/2+3,cy,5);}

  // Füze / silah
  g.fillStyle(0xddaa33);
  g.fillRect(cx-ws*.28,cy-bh*.5-3,8,2);
  g.fillRect(cx-ws*.28,cy+bh*.5+1,8,2);
}

function mkHeliTex(g,v,TW,TH){
  const cx=TW/2,cy=TH/2,bc=v.bc,tc=v.tc||bc;

  // Ana rotor gölgesi
  g.fillStyle(0x222222,.5);g.fillRect(cx-26,cy-15,52,2);
  // Ana rotor
  g.fillStyle(0x444444);g.fillRect(cx-26,cy-14,52,2);
  g.fillStyle(0x666666);g.fillRect(cx-26,cy-14,52,1);

  // Gövde (heli silueti)
  if(v.slim){
    g.fillStyle(bc);g.fillEllipse(cx-2,cy,38,11);
    g.fillStyle(tc);g.fillEllipse(cx+4,cy,18,8);
  }else{
    g.fillStyle(bc);g.fillEllipse(cx-2,cy,44,15);
    g.fillStyle(tc);g.fillEllipse(cx+4,cy,22,11);
    // Arka kısım (heli kuyruk kirişi)
    g.fillStyle(bc);g.fillRect(cx+18,cy-3,20,5);
  }

  // Kuyruk kirişi (her heli için)
  g.fillStyle(bc);g.fillRect(cx+18,cy-2,20,4);
  // Küçük kuyruk rotoru
  g.fillStyle(0x444444);g.fillRect(cx+36,cy-8,2,12);
  g.fillStyle(0x666666);g.fillRect(cx+36,cy-7,2,11);

  // Kokpit cam
  g.fillStyle(0x44aadd,.9);
  if(v.slim){g.fillEllipse(cx-8,cy,14,9);}else{g.fillEllipse(cx-10,cy,16,12);}

  // Silah podları (savaş helikopteri)
  if(v.pods){
    g.fillStyle(0x334422);
    g.fillRect(cx-6,cy+5,14,5);
    // Rocket pod
    g.fillStyle(0x222222);
    g.fillRect(cx-5,cy+6,3,3);g.fillRect(cx-1,cy+6,3,3);g.fillRect(cx+3,cy+6,3,3);
    // Top namlusu
    g.fillStyle(0x111111);g.fillRect(cx-16,cy-1,6,2);
  }

  // İniş takımı
  g.fillStyle(0x333333);
  g.fillRect(cx-10,cy+6,5,4);g.fillRect(cx+5,cy+6,5,4);
  g.fillRect(cx-12,cy+10,24,2);

  // Yan markası
  g.fillStyle(tc);g.fillRect(cx-4,cy-4,12,3);
}

function mkDroneTex(g,v,TW,TH){
  const cx=TW/2,cy=TH/2,ws=v.ws||50,bc=v.bc;

  // Quad drone (4 rotor)
  if(v.quad){
    g.fillStyle(bc);g.fillRect(cx-5,cy-5,10,10);
    // Çapraz kollar
    g.fillStyle(0x333333);
    g.fillRect(cx-18,cy-2,36,4);g.fillRect(cx-2,cy-18,4,36);
    // Rotorlar
    g.fillStyle(0x555555);
    g.fillCircle(cx-16,cy-16,5);g.fillCircle(cx+16,cy-16,5);
    g.fillCircle(cx-16,cy+16,5);g.fillCircle(cx+16,cy+16,5);
    g.fillStyle(0x999999);
    g.fillRect(cx-21,cy-17,10,2);g.fillRect(cx+11,cy-17,10,2);
    g.fillRect(cx-21,cy+15,10,2);g.fillRect(cx+11,cy+15,10,2);
    g.fillStyle(0x80c8e0);g.fillCircle(cx,cy,3);
    return;
  }

  // Stealth drone (B-2 tarzı flying wing)
  if(v.stl){
    g.fillStyle(bc);
    // Ana flying wing üçgeni
    g.fillTriangle(cx-ws/2,cy+6, cx,cy-6, cx+ws/2,cy+6);
    g.fillTriangle(cx-ws/2,cy+6, cx-ws/2+8,cy-2, cx,cy-6);
    g.fillTriangle(cx+ws/2,cy+6, cx+ws/2-8,cy-2, cx,cy-6);
    // Açık kenar kesme
    g.fillStyle(0x1a1a1a);g.fillTriangle(cx-ws/2+4,cy+5, cx-ws*.3,cy+5, cx-ws*.1,cy-1);
    g.fillTriangle(cx+ws/2-4,cy+5, cx+ws*.3,cy+5, cx+ws*.1,cy-1);
    // Cockpit/sensor
    g.fillStyle(0x224466);g.fillEllipse(cx,cy+2,ws*.18,5);
    return;
  }

  // Normal UCAV/drone (Bayraktar tarzı)
  g.fillStyle(bc);
  // Gövde
  g.fillEllipse(cx,cy,ws*.35,v.bh||7);
  // Kanatlar (düz, geniş)
  g.fillTriangle(cx-ws*.05,cy-2, cx-ws/2,cy+v.bh||3, cx+ws*.05,cy-2);
  g.fillTriangle(cx-ws*.05,cy+2, cx-ws/2,cy-v.bh+1||-2, cx+ws*.05,cy+2);
  // V-kuyruk
  g.fillStyle(0x888888);
  g.fillTriangle(cx+ws*.12,cy, cx+ws*.38,cy-8, cx+ws*.3,cy);
  g.fillTriangle(cx+ws*.12,cy, cx+ws*.38,cy+8, cx+ws*.3,cy);
  // İtici pervane (arka)
  if(v.pusher!==false){g.fillStyle(0x555555);g.fillCircle(cx+ws*.15,cy,4);g.fillStyle(0x777777);g.fillRect(cx+ws*.12,cy-5,ws*.06,2);g.fillRect(cx+ws*.12,cy+3,ws*.06,2);}
  // Optik sensör
  g.fillStyle(0x224466,.9);g.fillCircle(cx-ws*.08,cy,4);
  g.fillStyle(0x88ddff,.7);g.fillCircle(cx-ws*.08,cy,2);
}

function mkSniperTex(g,v,TW,TH){
  const cx=TW/2,cy=TH/2+4;
  // Ghillie suit vücudu (pürüzlü doku)
  g.fillStyle(v.bc);g.fillRect(cx-5,cy+4,4,10);g.fillRect(cx+1,cy+4,4,10);
  g.fillStyle(v.bc);g.fillEllipse(cx,cy+2,13,16);
  // Ghillie doku — çalı dalları
  const gc=Phaser.Display.Color.ValueToColor(v.bc);
  g.fillStyle(Phaser.Display.Color.GetColor(gc.red+20,gc.green+15,gc.blue),0.6);
  g.fillRect(cx-7,cy-2,5,8);g.fillRect(cx+2,cy,4,7);g.fillRect(cx-4,cy+6,3,5);
  // Kafa + kask (ghillie örtülü)
  g.fillStyle(v.bc);g.fillEllipse(cx,cy-8,12,11);
  g.fillStyle(v.hc||v.bc);g.fillEllipse(cx,cy-9,10,8);
  // Scope dürbün — tüp
  g.fillStyle(0x111111);g.fillRect(cx-18,cy-4,26,3);
  // Namlu — uzun, ince
  g.fillStyle(0x333333);g.fillRect(cx-22,cy-3,30,2);
  g.fillStyle(0x555555);g.fillRect(cx-22,cy-3,8,1);
  // Bipod ayağı
  g.fillStyle(0x444444);g.fillRect(cx-20,cy-1,2,6);g.fillRect(cx-17,cy-1,2,6);
  // Scope lens
  g.fillStyle(0x8844cc,0.8);g.fillCircle(cx+4,cy-3,3);
  g.fillStyle(0xaaccff,0.6);g.fillCircle(cx+4,cy-3,1.5);
  // Çağa göre renk şeridi
  if(v.era>=3){g.fillStyle(0x222222,0.7);g.fillRect(cx-6,cy-6,12,4);}
}

function mkMedVehTex(g,v,TW,TH){
  const cx=TW/2,cy=TH/2,bc=v.bc,w=v.w||50,h=v.h||20;
  // Gövde (beyazımsı araç)
  g.fillStyle(bc);g.fillRect(cx-w/2,cy-h/2,w,h);
  // Ön / arka çerçeve
  g.fillStyle(0x889988);g.fillRect(cx-w/2,cy-h/2,4,h);g.fillRect(cx+w/2-4,cy-h/2,4,h);
  // Kırmızı haç — merkez
  g.fillStyle(0xff2222);
  g.fillRect(cx-10,cy-3,20,6); // yatay
  g.fillRect(cx-3,cy-10,6,20); // dikey
  // Haç çerçeve
  g.lineStyle(1,0xcc0000,0.8);
  g.strokeRect(cx-10,cy-3,20,6);g.strokeRect(cx-3,cy-10,6,20);
  // Tekerlekler
  g.fillStyle(0x222222);
  g.fillCircle(cx-w/2+8,cy+h/2-1,6);g.fillCircle(cx+w/2-8,cy+h/2-1,6);
  g.fillStyle(0x444444);
  g.fillCircle(cx-w/2+8,cy+h/2-1,3);g.fillCircle(cx+w/2-8,cy+h/2-1,3);
  // Çatı anteni
  g.fillStyle(0x555555);g.fillRect(cx+w/2-10,cy-h/2-5,2,6);
  // Ön cam
  g.fillStyle(0x88ccee,0.7);g.fillRect(cx+w/2-14,cy-h/2+3,8,h-6);
  // Kırmızı ışık bar
  g.fillStyle(0xff3333);g.fillRect(cx-w/2+5,cy-h/2-2,w-10,2);
}

function generateTextures(scene){
  const TW=72,TH=52;
  REG.forEach(def=>{
    const v=VP[def.id];if(!v)return;
    const g=scene.add.graphics();
    try{
      switch(v.fn){
        case 'inf':  mkInfTex(g,v,TW,TH);break;
        case 'tank': mkTankTex(g,v,TW,TH);break;
        case 'veh':  mkVehTex(g,v,TW,TH);break;
        case 'jet':  mkJetTex(g,v,TW,TH);break;
        case 'heli': mkHeliTex(g,v,TW,TH);break;
        case 'drone': mkDroneTex(g,v,TW,TH);break;
        case 'sniper':mkSniperTex(g,v,TW,TH);break;
        case 'medveh':mkMedVehTex(g,v,TW,TH);break;
      }
      g.generateTexture(def.id,TW,TH);
    }catch(e){}
    g.destroy();
  });
  // Turret texture
  const tg=scene.add.graphics();
  tg.fillStyle(0x556677);tg.fillRect(0,4,14,12);
  tg.fillStyle(0x667788);tg.fillRect(2,0,10,8);
  tg.fillStyle(0x445566);tg.fillRect(6,0,3,14);
  tg.generateTexture('turret',16,20);
  tg.destroy();
}

// ═══════════════════════════════════════════
//  DIFFICULTY & CAMPAIGN CONFIGS
// ═══════════════════════════════════════════
const DIFFICULTY={
  easy:  {aiGoldMult:.55,aiDecMult:1.8,waveInterval:38000,aiUltCdBase:50000,label:'KOLAY'},
  normal:{aiGoldMult:1.0,aiDecMult:1.0,waveInterval:28000,aiUltCdBase:30000,label:'NORMAL'},
  hard:  {aiGoldMult:1.55,aiDecMult:.65,waveInterval:18000,aiUltCdBase:18000,label:'ZOR'}
};
const CAMPAIGN_MISSIONS=[
  {title:'İLK ÇATIŞMA',   desc:'1990. Soğuk Savaş sona ererken sınırda gerilim tırmanıyor. TSK ilk hattı savunuyor.',   diff:'easy',  bonus:0},
  {title:'SERHAD MUHAREBESİ',desc:'2000. Modern silahlarla donanmış düşman hücuma geçiyor. Hattı koru!',              diff:'normal',bonus:200},
  {title:'TEKNOLOJİ SAVAŞI',desc:'2010. Drone\'lar ve akıllı sistemler savaşa girdi. Hava sahasını kontrol et.',        diff:'normal',bonus:400},
  {title:'DİJİTAL KUŞATMA', desc:'2020. Siber saldırılar ve ağır bombardıman. Son savunma hattı burada.',              diff:'hard',  bonus:600},
  {title:'GELECEK SAVAŞI',  desc:'2030. İnsansız sistemler, lazer silahları ve hipersonik füzeler. Son anda kazanılacak savaş.', diff:'hard',bonus:1000}
];

// ═══════════════════════════════════════════
//  GAME SCENE
// ═══════════════════════════════════════════
class GameScene extends Phaser.Scene{
  constructor(){super({key:'GameScene'});}

  preload(){
    generateTextures(this);
    Object.entries(PNG_URLS).forEach(([id,url])=>{if(url)this.load.image('png_'+id,url);});
  }

  create(){
    this.pAge=0;this.eAge=0;
    this.pGold=500;this.pXP=0;
    this.aiGold=500;this.aiXP=0;
    this.units=[];
    this.projs=[];
    this.fxTexts=[];
    this.particles=[];
    this.paused=false;
    this.gameOver=false;
    this.pUltCd=ULT_CD;
    this.aiUltCd=Phaser.Math.Between(18000,26000);
    this.incTimer=0;
    this.incInterval=3000;
    this.aiTimer=0;
    this.aiDecTimer=Phaser.Math.Between(1500,2800);
    this.baseDirty=true;
    // Yeni durum
    this.pKills=0;this.eKills=0;
    // Moral sistemi
    this.morale=70;// 0-100, 50=normal, 100=yüksek moral
    this._moralWarnT=false;
    this._lowHpWarned=false;
    this.craters=[];
    this.clouds=this._initClouds();
    this.waveTimer=0;
    this.waveInterval=28000;
    this.waveCount=0;
    this.repairCd=0;
    this.repairCdMax=12000;
    this.quizCd=0;
    this.quizCdMax=20000;
    this.pDefLineCd=0;
    this.eDefLineCd=0;
    this.totalTime=0;
    // Hız sistemi
    this.gameSpeed=1;
    this.simTime=0;
    // Kill streak
    this.streakCount=0;
    this.lastKillTime=0;
    this.totalDmgDealt=0;
    // Hava desteği
    this.airSupCd=0;
    this.airSupCdMax=45000;
    // Mayın sistemi
    this.mineCd=0;
    this.mineCdMax=8000;
    this.mines=[];
    // Takviye
    this.reinforceTimer=0;
    this.reinforceInterval=90000;
    this.reinforceCount=0;
    // Yağmur
    this.rainActive=false;
    this.rainTimer=0;
    this.rainInterval=Phaser.Math.Between(40000,80000);
    this.rainDuration=0;
    // Başarımlar
    this.achievements=new Set();
    // Kampanya
    this.campaignMode=false;
    this.campaignMission=0;
    // Difficulty (zorluk seçicide set edilir)
    const dKey=window._selectedDiff||'normal';
    const dCfg=DIFFICULTY[dKey];
    this._diffKey=dKey;
    this._aiGoldMult=dCfg.aiGoldMult;
    this._aiDecMult=dCfg.aiDecMult;
    this.waveInterval=dCfg.waveInterval;
    this._aiUltCdBase=dCfg.aiUltCdBase;
    this.aiUltCd=Phaser.Math.Between(this._aiUltCdBase*.7,this._aiUltCdBase);
    // EMP
    this.empCd=0;this.empCdMax=40000;
    this.empActive=false;this.empTimer=0;
    this.empSlowedUnits=new Set();
    // Ağır topçu
    this.artyCd=0;this.artyCdMax=70000;
    // Altın faizi
    this.interestTimer=0;this.interestInterval=30000;
    // Boss birimi
    this.bossSpawnedAges=new Set();
    // Gece modu
    this.nightMode=false;
    // Hedef öncelik modu
    this.targetPriority='nearest';
    // Araştırma ağacı
    this.research={war:{},def:{},eco:{}};
    this._resTab='war';
    // Demir Perde
    this.ironCurtainCd=0;this.ironCurtainCdMax=60000;
    this.ironCurtainActive=false;this.ironCurtainTimer=0;
    // Topçu nişan modu
    this.artilleryMode=false;
    // Dalga önizleme
    this._wavePreviewTimer=0;
    // Dinamik gökyüzü zamanı
    this._skyTime=0;
    // Dinamik hava durumu döngüsü
    this.weatherTimer=0;this.weatherInterval=90000;
    this.weatherStates=['clear','rain','cloudy','clear','clear'];
    this.weatherIdx=0;this.curWeather='clear';
    this.rainDrops=[];
    // Savaş müziği
    this._musicNodes=[];
    // Dalga hayatta kalma bonusu
    this.lastWaveKills=0;
    this.waveIncomeBoost=0;
    // Yeni yetenekler: cooldown + state
    this.droneCd=0;this.droneCdMax=45000;
    this.fogCd=0;this.fogCdMax=35000;this.fogActive=false;this.fogTimer=0;
    this.nukeCd=0;this.nukeUsed=false;
    this.logiCd=0;this.logiCdMax=40000;
    this.saboCd=0;this.saboCdMax=60000;this.saboActive=false;this.saboTimer=0;
    // Bölge kontrolü
    this.zoneOwner=null;this.zoneTimer=0;this.zoneCaptureTime=8000;this.zoneGoldTimer=0;
    // Altın sandıkları
    this.goldCrates=[];this.crateTimer=0;this.crateInterval=22000;
    // Birim morali
    this.panicUnits=new Set();
    // Skor çarpanı
    this.scoreMultiplier=1;this.scoreMultTimer=0;
    // Dinamik zorluk
    this.dynDiffTimer=0;this._dynDiffLevel=0;
    // Otomatik kayıt
    this.autoSaveTimer=0;
    // Kill kutlama eşikleri
    this._lastKillCelebrate=0;
    // Yıldız partikülleri (gece modu)
    this._stars=Array.from({length:60},()=>({x:Math.random()*W,y:Math.random()*H*.6,r:Math.random()*1.5+.3,t:Math.random()*Math.PI*2}));
    // HP bar görünürlük
    this.showHPBars=true;
    // Tarihi olaylar
    this.eventTimer=0;this.eventInterval=55000;
    // Kill feed
    this._killFeedCount=0;
    // Taret yükseltme seviyesi
    this.turretUpgLevel=0;
    // Mission bonus altın
    const mBonus=window._missionBonus||0;
    this.pGold+=mBonus;
    SFX.init();

    const hp=AGES[0].bHP;
    this.pBase={hp,maxHP:hp,x:PBX};
    this.eBase={hp,maxHP:hp,x:EBX};

    // Initial turrets (1 each side)
    this.pTurrets=[this._makeTurret('p',0)];
    this.eTurrets=[this._makeTurret('e',0)];

    this.bgGfx=this.add.graphics();
    this.cloudGfx=this.add.graphics();
    this.craterGfx=this.add.graphics();
    this.baseGfx=this.add.graphics();
    this.turretGfx=this.add.graphics();
    this.projGfx=this.add.graphics();
    this.fxGfx=this.add.graphics();
    this.hpGfx=this.add.graphics();

    this.drawBG();
    this.drawBases();
    this.buildSpawnPanel();

    window._tryUpgrade=()=>this.tryUpgrade();
    window._openQuiz=()=>this.openQuiz();
    window._closeQuiz=()=>this.closeQuiz();
    window._spawnUnit=(id,bulk)=>this.spawnPlayerUnit(id,bulk);
    window._fireUlt=()=>this.playerFireUlt();
    window._buyTurret=()=>this.buyTurret();
    window._repairBase=()=>this.repairBase();
    window._toggleSound=()=>this.toggleSound();
    window._toggleSpeed=()=>this.toggleSpeed();
    window._airSupport=()=>this.airSupport();
    window._placeMine=()=>this.placeMine();
    window._toggleStats=()=>this.toggleStats();
    window._continuecamp=()=>this.continueCampaign();
    window._empBlast=()=>this.empBlast();
    window._heavyArty=()=>this.heavyArty();
    window._toggleNight=()=>this.toggleNight();
    window._toggleMusic=()=>this.toggleMusic();
    window._droneSwarm=()=>this.droneSwarm();
    window._fogBomb=()=>this.fogBomb();
    window._nuclearStrike=()=>this.nuclearStrike();
    window._logisticSupport=()=>this.logisticSupport();
    window._economicSabotage=()=>this.economicSabotage();
    window._resumeGame=()=>this.resumeGame();
    window._restartGame=()=>this.restartGame();
    window._toggleHelp=()=>this.toggleHelp();
    window._upgradeTurret=()=>this.upgradeTurret();
    window._cycleTarget=()=>this.cycleTargetPriority();
    window._toggleResearch=()=>this.toggleResearch();
    window._resTab=(t)=>this.setResTab(t);
    window._buyResearch=(cat,id)=>this.buyResearch(cat,id);
    window._ironCurtain=()=>this.activateIronCurtain();
    window._toggleArtilleryMode=()=>this.toggleArtilleryMode();
    window._toggleCmdDrawer=()=>{
      const d=document.getElementById('cmd-drawer');
      const t=document.getElementById('btn-cmd-toggle');
      if(!d||!t)return;
      const open=d.classList.toggle('open');
      t.classList.toggle('open',open);
      t.textContent=open?'🎮 KOMUTA ▴':'🎮 KOMUTA ▾';
    };
    // Kampanya bonus uygula
    if(this.campaignMode&&window._campaignMission>0){
      showToast('🎖 Kampanya Görevi '+(window._campaignMission+1)+': +'+mBonus+'₺ bonus altın','#f39c12');
    }
    // Cinematic açılış
    this._showCinematic();
    // Klavye olayları
    this.input.keyboard.on('keydown-TAB',e=>{e.preventDefault();this.toggleStats();});
    this.input.keyboard.on('keydown-S',e=>{if(!this.paused)this.toggleStats();});
    this.input.keyboard.on('keydown-ESC',()=>{
      const sp=document.getElementById('stats-panel');
      if(sp&&sp.classList.contains('open')){this.toggleStats();return;}
      const hp=document.getElementById('help-overlay');
      if(hp&&hp.classList.contains('open')){this.toggleHelp();return;}
      this.togglePause();
    });
    this.input.keyboard.on('keydown-P',()=>this.togglePause());
    this.input.keyboard.on('keydown-R',()=>this.restartGame());
    this.input.keyboard.on('keydown-N',()=>this.toggleNight());
    this.input.keyboard.on('keydown-M',()=>this.toggleMusic());
    // Pointer move: hover tooltip
    this.input.on('pointermove',ptr=>this._onPointerMove(ptr.worldX,ptr.worldY));
    // Pointer down: sağ tık = birim sat, sol tık = topçu nişan modu
    this.input.on('pointerdown',ptr=>{
      if(ptr.rightButtonDown()){this._sellUnit(ptr.worldX,ptr.worldY);return;}
      if(this.artilleryMode&&!ptr.rightButtonDown()){
        const wx=ptr.worldX;
        if(wx>W/2){// Sadece düşman yarısında
          this._fireClickArtillery(wx,ptr.worldY);
        }
      }
    },this);
    // Yüksek skor tablosunu göster
    this._renderHiScores();
    this.updateHUD();
  }

  _makeTurret(side,slotIdx){
    const def=TURRET_DEFS[side==='p'?this.pAge:this.eAge]||TURRET_DEFS[0];
    const pos=(side==='p'?PTUR_POS:ETUR_POS)[slotIdx];
    // lastAtk'yi rastgele offsetle başlat → her taret farklı zamanda ateşler
    const offset=Math.floor(Math.random()*def.rt);
    const age=side==='p'?this.pAge:this.eAge;
    return{side,slotIdx,age,x:pos.x,y:pos.y,hp:def.hp,maxHP:def.hp,dmg:def.dmg,rng:def.rng,rt:def.rt,lastAtk:-offset,alive:true,aimAngle:-Math.PI/2};
  }

  _initClouds(){
    return Array.from({length:7},()=>({
      x:Math.random()*W,
      y:30+Math.random()*95,
      w:65+Math.random()*90,
      h:18+Math.random()*14,
      speed:9+Math.random()*14,
      alpha:0.09+Math.random()*0.08
    }));
  }

  drawClouds(){
    const g=this.cloudGfx;
    for(const c of this.clouds){
      g.fillStyle(0xffffff,c.alpha);
      g.fillEllipse(c.x,c.y,c.w,c.h);
      g.fillEllipse(c.x+c.w*.24,c.y-c.h*.44,c.w*.55,c.h*.8);
      g.fillEllipse(c.x-c.w*.21,c.y-c.h*.33,c.w*.48,c.h*.7);
    }
  }

  spawnCrater(x){
    if(Math.random()>0.65)return;
    const cx=x+Phaser.Math.Between(-9,9);
    const w=16+Math.random()*13;
    const h=5+Math.random()*4;
    this.craters.push({x:cx,w,h});
    if(this.craters.length>22)this.craters.shift();
    this.craterGfx.clear();
    for(const c of this.craters){
      this.craterGfx.fillStyle(0x080808,0.62);
      this.craterGfx.fillEllipse(c.x,GND+4,c.w,c.h);
      this.craterGfx.fillStyle(0x1a1010,0.28);
      this.craterGfx.fillEllipse(c.x,GND+4,c.w*1.5,c.h*1.5);
    }
  }

  spawnExplosionSparks(x,y){
    const count=5+Math.floor(Math.random()*6);
    for(let i=0;i<count;i++){
      const angle=Math.random()*Math.PI*2;
      const spd=55+Math.random()*110;
      this.particles.push({x,y,vx:Math.cos(angle)*spd/100,vy:Math.sin(angle)*spd/100-1.4,life:280+Math.random()*320,maxLife:600,type:'spark'});
    }
    // Yanık zemin izi (kara toprak leke, 12 saniye)
    this.particles.push({x,y:GND,r:18+Math.random()*12,life:12000,maxLife:12000,type:'scorch'});
  }

  spawnTracer(x1,y1,x2,y2,col){
    // Bullet tracer: gidiş yönünde kısa parlak çizgi
    const dx=x2-x1,dy=y2-y1,dist=Math.sqrt(dx*dx+dy*dy)||1;
    const spd=600;
    this.particles.push({x:x1,y:y1,vx:(dx/dist)*spd,vy:(dy/dist)*spd,life:80,maxLife:80,type:'tracer',col:col||0xffee88});
  }

  drawBG(){
    const g=this.bgGfx;g.clear();

    // ── Dinamik gökyüzü — gün döngüsü (120s tam döngü) ──
    const dayT=(this._skyTime||0)%120000/120000; // 0→1
    // Renk keyframe'leri: şafak(0) → gündüz(.25) → akşam(.5) → gece(.75) → şafak(1)
    const skyKeys=[
      {t:0,   r1:80, g1:30,  b1:10,  r2:120,g2:70, b2:30},  // şafak
      {t:0.25,r1:15, g1:40,  b1:90,  r2:60, g2:90, b2:140}, // gündüz
      {t:0.5, r1:90, g1:25,  b1:10,  r2:140,g2:60, b2:20},  // akşam
      {t:0.75,r1:5,  g1:5,   b1:20,  r2:15, g2:20, b2:50},  // gece
      {t:1,   r1:80, g1:30,  b1:10,  r2:120,g2:70, b2:30},
    ];
    let k0=skyKeys[0],k1=skyKeys[1];
    for(let i=0;i<skyKeys.length-1;i++){if(dayT>=skyKeys[i].t&&dayT<=skyKeys[i+1].t){k0=skyKeys[i];k1=skyKeys[i+1];break;}}
    const kT=(dayT-k0.t)/Math.max(0.001,k1.t-k0.t);
    const lerp=(a,b,t)=>Math.round(a+(b-a)*t);
    const cr1=lerp(k0.r1,k1.r1,kT),cg1=lerp(k0.g1,k1.g1,kT),cb1=lerp(k0.b1,k1.b1,kT);
    const cr2=lerp(k0.r2,k1.r2,kT),cg2=lerp(k0.g2,k1.g2,kT),cb2=lerp(k0.b2,k1.b2,kT);
    for(let y=0;y<GND;y++){
      const t=y/GND;
      const r=Math.round(cr1+(cr2-cr1)*t),gr=Math.round(cg1+(cg2-cg1)*t),b=Math.round(cb1+(cb2-cb1)*t);
      g.fillStyle(Phaser.Display.Color.GetColor(r,gr,b));g.fillRect(0,y,W,1);
    }
    // ── Ufuk ışıması ──
    const isEvening=dayT>=0.4&&dayT<=0.65;
    const isDawn=dayT<=0.1||dayT>=0.9;
    if(isEvening||isDawn){
      for(let y=GND-65;y<GND;y++){
        const t=(y-(GND-65))/65;
        g.fillStyle(Phaser.Display.Color.GetColor(140,55,10),t*.22);g.fillRect(0,y,W,1);
      }
    }

    // ── Yıldızlar ──
    [[45,28],[130,18],[220,42],[310,15],[430,32],[520,11],[640,38],[750,22],[850,44],[980,17],[1090,35],[1160,25],[80,55],[350,62],[700,58],[1000,50],[180,8],[560,5],[900,12]].forEach(([sx,sy])=>{
      g.fillStyle(0xffffff,0.35+Math.random()*.4);g.fillCircle(sx,sy,Math.random()<0.3?1.5:1);
    });

    // ── Arka dağ silüeti (çok katmanlı) ──
    // Uzak dağlar (açık)
    g.fillStyle(0x384060,.7);
    const far=[[0,GND],[0,GND-60],[80,GND-95],[200,GND-70],[340,GND-110],[480,GND-80],[600,GND-125],[720,GND-90],[860,GND-115],[980,GND-75],[1100,GND-100],[1200,GND-65],[1200,GND]];
    g.fillPoints(far.map(([x,y])=>({x,y})),true);
    // Yakın dağlar (koyu)
    g.fillStyle(0x28303a);
    const near=[[0,GND],[0,GND-40],[120,GND-80],[260,GND-55],[400,GND-90],[540,GND-60],[660,GND-100],[800,GND-70],[940,GND-88],[1060,GND-55],[1200,GND-45],[1200,GND]];
    g.fillPoints(near.map(([x,y])=>({x,y})),true);

    // ── Şehir/köy silueti (arka planda) ──
    g.fillStyle(0x1e2430,.85);
    // Sol bloklar
    [[30,GND-30,22,30],[60,GND-48,18,48],[88,GND-35,24,35],[140,GND-55,16,55],[165,GND-42,20,42],[200,GND-28,28,28]].forEach(([bx,by,bw,bh])=>{
      g.fillRect(bx,by,bw,bh);
      // Pencereler (sarı ışık)
      g.fillStyle(0xffdd88,.5);
      for(let wx=bx+3;wx<bx+bw-4;wx+=6){for(let wy=by+4;wy<by+bh-4;wy+=8){if(Math.random()>.35)g.fillRect(wx,wy,3,4);}}
      g.fillStyle(0x1e2430,.85);
    });
    // Sağ bloklar
    [[950,GND-32,20,32],[980,GND-52,16,52],[1008,GND-38,22,38],[1050,GND-60,18,60],[1078,GND-44,24,44],[1120,GND-30,30,30]].forEach(([bx,by,bw,bh])=>{
      g.fillRect(bx,by,bw,bh);
      g.fillStyle(0xffdd88,.5);
      for(let wx=bx+3;wx<bx+bw-4;wx+=6){for(let wy=by+4;wy<by+bh-4;wy+=8){if(Math.random()>.35)g.fillRect(wx,wy,3,4);}}
      g.fillStyle(0x1e2430,.85);
    });

    // ── Zemin katmanları ──
    g.fillStyle(0x364428);g.fillRect(0,GND,W,H-GND);       // ana toprak
    g.fillStyle(0x445535);g.fillRect(0,GND,W,6);             // çim şeridi
    g.fillStyle(0x2a321e);g.fillRect(0,GND+6,W,10);          // toprak geçiş

    // ── Yol (orta harita) ──
    g.fillStyle(0x4a4030);g.fillRect(W/2-22,GND,44,H-GND);  // asfalt
    g.fillStyle(0x605848);g.fillRect(W/2-20,GND,40,H-GND);
    g.fillStyle(0xffeeaa,.55);                                // orta çizgi
    for(let ry=GND+4;ry<H;ry+=20)g.fillRect(W/2-2,ry,4,10);

    // ── Kum çuvalları (oyuncu savunma hattı) ──
    g.fillStyle(0x9a8050);
    for(let bx=PDEF_LINE-30;bx<=PDEF_LINE+30;bx+=12){
      g.fillEllipse(bx,GND+5,14,9);g.fillEllipse(bx+6,GND,14,9);
    }
    g.fillStyle(0x8a7040,.8);
    for(let bx=PDEF_LINE-30;bx<=PDEF_LINE+30;bx+=12)g.fillEllipse(bx,GND+5,14,4);

    // ── Tel örgü (düşman savunma hattı) ──
    g.lineStyle(1,0x888855,.7);
    g.lineBetween(EDEF_LINE-35,GND-14,EDEF_LINE+35,GND-14);
    for(let bx=EDEF_LINE-35;bx<=EDEF_LINE+35;bx+=8){
      g.lineBetween(bx,GND-18,bx+4,GND-8);
    }
    // Tel çivilar
    g.lineStyle(1,0xaaaa66,.5);
    for(let bx=EDEF_LINE-35;bx<=EDEF_LINE+35;bx+=16){
      g.lineBetween(bx,GND-14,bx-4,GND-20);g.lineBetween(bx,GND-14,bx+4,GND-20);
    }

    // ── Bulutlar ──
    [[120,50],[350,34],[620,58],[880,40],[1060,52]].forEach(([cx,cy])=>{
      g.fillStyle(0xffffff,.15);g.fillEllipse(cx,cy,85,24);
      g.fillStyle(0xffffff,.1);g.fillEllipse(cx+28,cy-8,60,18);g.fillEllipse(cx-24,cy-6,52,16);
    });

    // ── Savunma hatları ──
    g.lineStyle(1,0x4488ff,.45);
    for(let ly=20;ly<GND-16;ly+=13)g.lineBetween(PDEF_LINE,ly,PDEF_LINE,ly+7);
    g.fillStyle(0x4488ff,.6);g.fillTriangle(PDEF_LINE-5,GND-16,PDEF_LINE+5,GND-16,PDEF_LINE,GND-28);
    g.fillStyle(0x4488ff,.12);g.fillRect(PDEF_LINE-1,0,2,GND);

    g.lineStyle(1,0xff4444,.45);
    for(let ly=20;ly<GND-16;ly+=13)g.lineBetween(EDEF_LINE,ly,EDEF_LINE,ly+7);
    g.fillStyle(0xff4444,.6);g.fillTriangle(EDEF_LINE-5,GND-16,EDEF_LINE+5,GND-16,EDEF_LINE,GND-28);
    g.fillStyle(0xff4444,.12);g.fillRect(EDEF_LINE-1,0,2,GND);

    // ── Taşlar ve ot demetleri (zemin detay) ──
    [[180,GND+8],[290,GND+5],[450,GND+9],[680,GND+6],[820,GND+8],[1020,GND+7]].forEach(([rx,ry])=>{
      g.fillStyle(0x4a4a3a,.7);g.fillEllipse(rx,ry,10,5);
    });
  }

  drawBases(){
    const g=this.baseGfx;g.clear();
    const px=PBX;
    // TSK Hangar — taş/beton doku, metal çatı
    g.fillStyle(0x606878);g.fillRect(px-65,GND-90,130,90);
    g.fillStyle(0x4a5560);g.fillEllipse(px,GND-90,130,50);
    // Beton panel çizgileri
    g.lineStyle(1,0x505868,.6);
    for(let i=0;i<3;i++){g.lineBetween(px-65,GND-70+i*20,px+65,GND-70+i*20);}
    g.lineBetween(px-20,GND-90,px-20,GND);g.lineBetween(px+20,GND-90,px+20,GND);
    // Kapı
    g.fillStyle(0x383e48);g.fillRect(px-35,GND-60,70,60);
    g.fillStyle(0x505860);
    for(let d=-34;d<36;d+=12){g.fillRect(px+d,GND-59,10,58);}
    // Kapı menteşe detayı
    g.fillStyle(0x667788);g.fillCircle(px-36,GND-50,3);g.fillCircle(px-36,GND-30,3);
    // Kontrol kulesi
    g.fillStyle(0x6a7280);g.fillRect(px+40,GND-115,22,115);
    g.fillStyle(0x4a5560);g.fillRect(px+36,GND-125,30,14);
    // Cam
    g.fillStyle(0x80c0e0);g.setAlpha(.85);g.fillRect(px+39,GND-122,7,9);g.fillRect(px+50,GND-122,7,9);g.setAlpha(1);
    // Anten
    g.fillStyle(0xaaaa44);g.fillRect(px+52,GND-143,2,20);g.fillCircle(px+53,GND-143,3);
    // Türk bayrağı
    g.fillStyle(0xcc0000);g.fillRect(px-58,GND-115,26,17);
    g.fillStyle(0xffffff);g.fillCircle(px-49,GND-107,4);g.fillCircle(px-45,GND-107,5);
    g.fillStyle(0xcc0000);g.fillCircle(px-45,GND-107,3.5);
    // Çit
    g.fillStyle(0x445566);for(let fx=-72;fx<80;fx+=10){g.fillRect(px+fx,GND-14,2,14);}
    g.lineStyle(1,0x556677,1);g.lineBetween(px-72,GND-8,px+80,GND-8);

    const ex=EBX;
    // Düşman sığınağı — çöl betonu, taş doku
    g.fillStyle(0x9a8060);g.fillRect(ex-62,GND-75,124,75);
    // Çatı panelleri
    g.fillStyle(0x8a7050);g.fillRect(ex-62,GND-82,124,10);
    g.lineStyle(1,0x7a6040,.7);
    for(let i=0;i<3;i++){g.lineBetween(ex-62,GND-60+i*18,ex+62,GND-60+i*18);}
    // Mazgallar
    g.fillStyle(0x403020);
    [[ex-50,GND-60],[ex-22,GND-60],[ex+8,GND-60],[ex+36,GND-60]].forEach(([bx,by])=>{
      g.fillRect(bx,by,16,13);g.fillStyle(0x221810);g.fillCircle(bx+8,by+6,3);g.fillStyle(0x403020);
    });
    // Siperler (kumtorbası)
    const sbc=[0xa08060,0x907050,0xb09070];
    for(let si=0;si<8;si++){
      g.fillStyle(sbc[si%3]);
      g.fillEllipse(ex-58+si*16,GND-12,18,10);
      if(si<7)g.fillEllipse(ex-50+si*16,GND-20,18,10);
      if(si<6)g.fillEllipse(ex-42+si*16,GND-28,18,10);
    }
    // Anten kulesi
    g.fillStyle(0x777060);g.fillRect(ex-68,GND-105,8,105);
    g.fillStyle(0x888070);g.fillEllipse(ex-64,GND-110,22,13);
    // Bayrak
    g.fillStyle(0x880000);g.fillRect(ex+52,GND-110,24,16);
    g.fillStyle(0x222222);g.fillRect(ex+52,GND-94,24,8);
    g.fillStyle(0xddcc00);g.fillCircle(ex+64,GND-103,4);
    g.lineStyle(2,0xddcc00,1);g.lineBetween(ex+60,GND-97,ex+68,GND-97);
    this.baseDirty=false;
    this.drawTurretGraphics();
  }

  drawTurretGraphics(){
    const g=this.turretGfx;g.clear();
    const all=[...this.pTurrets,...this.eTurrets];
    for(const t of all){
      if(!t.alive)continue;
      const pct=t.hp/t.maxHP;
      const pc=t.side==='p';
      const age=t.age||0;
      const angle=t.aimAngle!==undefined?t.aimAngle:(pc?-Math.PI*.38:-Math.PI*.62);
      this._drawTurretByAge(g,t,age,pc,angle);
      // HP çubuğu
      g.fillStyle(0x111111);g.fillRect(t.x-12,t.y-27,24,4);
      g.fillStyle(pct>0.5?0x22cc44:pct>0.25?0xffaa22:0xff3322);
      g.fillRect(t.x-12,t.y-27,24*pct,4);
      g.lineStyle(1,0x555555,1);g.strokeRect(t.x-12,t.y-27,24,4);
    }
  }

  _drawTurretByAge(g,t,age,pc,angle){
    const cx=t.x,cy=t.y;
    const bx=cx+Math.cos(angle),by=cy-7+Math.sin(angle);

    if(age===0){
      // Kum torbası mevzii
      g.fillStyle(0x7a6040,.95);g.fillEllipse(cx,cy+5,32,12);
      g.fillStyle(0x8a7050);g.fillEllipse(cx-8,cy+1,16,9);g.fillEllipse(cx+8,cy+1,16,9);g.fillEllipse(cx,cy-2,16,9);
      g.fillStyle(0x6a6a7a);g.fillCircle(cx,cy-6,7);
      g.fillStyle(0x8a8aaa);g.fillCircle(cx,cy-6,5);
      const bl=17;
      g.lineStyle(4,0x222,1);g.lineBetween(cx,cy-6,cx+Math.cos(angle)*bl,cy-6+Math.sin(angle)*bl);
      g.lineStyle(2,0x555,1);g.lineBetween(cx,cy-6,cx+Math.cos(angle)*bl,cy-6+Math.sin(angle)*bl);
      g.fillStyle(0x333);g.fillCircle(cx+Math.cos(angle)*bl,cy-6+Math.sin(angle)*bl,2.5);

    }else if(age===1){
      // ZU-23 Çift Namlu AA
      g.fillStyle(0x333333);g.fillRect(cx-12,cy+4,24,6);
      for(let i=0;i<4;i++)g.fillCircle(cx-8+i*5,cy+10,3);
      g.fillStyle(pc?0x3d5c2e:0x5c3d1e);g.fillRect(cx-9,cy-1,18,9);
      g.fillStyle(pc?0x4a7040:0x70401a);g.fillRect(cx-7,cy,4,6);g.fillRect(cx+3,cy,4,6);
      g.fillStyle(pc?0x4a6888:0x886448);g.fillCircle(cx,cy-8,9);
      g.fillStyle(pc?0x5a78a8:0xa87858);g.fillCircle(cx,cy-8,6);
      const o=3.5,bl=20;
      const ox=Math.cos(angle+Math.PI/2)*o,oy=Math.sin(angle+Math.PI/2)*o;
      const ex=cx+Math.cos(angle)*bl,ey=cy-8+Math.sin(angle)*bl;
      g.lineStyle(3,0x222,1);g.lineBetween(cx+ox,cy-8+oy,ex+ox,ey+oy);g.lineBetween(cx-ox,cy-8-oy,ex-ox,ey-oy);
      g.lineStyle(1,0x666,1);g.lineBetween(cx+ox,cy-8+oy,ex+ox,ey+oy);g.lineBetween(cx-ox,cy-8-oy,ex-ox,ey-oy);
      g.fillStyle(pc?0x3a5070:0x703a20);
      const sa=angle+Math.PI/2;g.fillRect(cx+Math.cos(sa)*12-3,cy-8+Math.sin(sa)*12-7,6,14);

    }else if(age===2){
      // Oerlikon 35mm Zırhlı
      g.fillStyle(0x2a2a2a);g.fillRect(cx-13,cy+2,26,8);
      g.lineStyle(1,0x444,1);for(let i=0;i<5;i++)g.lineBetween(cx-12+i*6,cy+2,cx-12+i*6,cy+10);
      g.fillStyle(pc?0x4a5e3a:0x5e3a2a);
      g.fillTriangle(cx-11,cy+2,cx+11,cy+2,cx+11,cy-8);g.fillRect(cx-11,cy-8,22,12);
      const ss=pc?-14:14;
      g.fillStyle(pc?0x607848:0x786048);g.fillRect(cx+ss,cy-11,5,18);
      g.fillStyle(pc?0x4a6080:0x80604a);g.fillEllipse(cx,cy-11,18,13);
      const bl=21,bex=cx+Math.cos(angle)*bl,bey=cy-11+Math.sin(angle)*bl;
      g.lineStyle(7,0x222,1);g.lineBetween(cx,cy-11,bex,bey);
      g.lineStyle(5,0x444,1);g.lineBetween(cx,cy-11,bex,bey);
      g.lineStyle(3,0x666,1);g.lineBetween(cx,cy-11,bex,bey);
      g.fillStyle(0x2a2a2a);g.fillCircle(bex,bey,5);g.fillStyle(0x555);g.fillCircle(bex,bey,3);

    }else if(age===3){
      // CIWS Phalanx Gatling
      g.fillStyle(pc?0x3a4a5a:0x5a3a2a);g.fillRect(cx-13,cy+1,26,9);
      g.fillStyle(0x2a2a2a);for(let i=0;i<4;i++)g.fillCircle(cx-9+i*6,cy+10,3.5);
      g.fillStyle(pc?0x506888:0x886850);g.fillCircle(cx,cy-7,12);
      g.fillStyle(pc?0x607898:0x987860);g.fillEllipse(cx,cy-10,18,10);
      const rd=pc?9:-9;
      g.fillStyle(0x555555);g.fillEllipse(cx+rd,cy-15,12,6);
      g.lineStyle(1,0x888,1);g.lineBetween(cx+rd,cy-15,cx+rd,cy-20);g.lineBetween(cx+rd-4,cy-20,cx+rd+4,cy-20);
      const bl=22;
      for(let i=-1;i<=1;i++){
        const ox=Math.cos(angle+Math.PI/2)*i*3,oy=Math.sin(angle+Math.PI/2)*i*3;
        g.lineStyle(i===0?3:2,i===0?0x555:0x444,1);
        g.lineBetween(cx+ox,cy-7+oy,cx+Math.cos(angle)*bl+ox,cy-7+Math.sin(angle)*bl+oy);
      }
      g.fillStyle(0x333);g.fillCircle(cx,cy-7,4);

    }else{
      // Age 4 — Lazer Savunma
      g.fillStyle(pc?0x1a2a3a:0x3a1a0a);
      g.fillTriangle(cx-13,cy+8,cx+13,cy+8,cx+(pc?6:-6),cy+1);g.fillRect(cx-11,cy-5,22,13);
      g.lineStyle(2,pc?0x0088ff:0xff4400,.8);g.strokeCircle(cx,cy-8,11);
      g.lineStyle(1,pc?0x00ccff:0xff8800,.35);g.strokeCircle(cx,cy-8,14);
      g.fillStyle(pc?0x0099ff:0xff5500,.9);g.fillCircle(cx,cy-8,6);
      g.fillStyle(0xffffff,.75);g.fillCircle(cx-1,cy-9,2.5);
      const bl=23,lx=cx+Math.cos(angle)*bl,ly=cy-8+Math.sin(angle)*bl;
      g.lineStyle(5,pc?0x002299:0x990000,.9);g.lineBetween(cx,cy-8,lx,ly);
      g.lineStyle(3,pc?0x0055ff:0xff2200,.8);g.lineBetween(cx,cy-8,lx,ly);
      g.lineStyle(1,pc?0x88ccff:0xff9966,.5);g.lineBetween(cx,cy-8,lx,ly);
      g.fillStyle(pc?0x0055ff:0xff2200,.25);g.fillCircle(lx,ly,8);
      g.fillStyle(pc?0x0055ff:0xff2200,.35);g.fillCircle(cx,cy-8,7);
    }
  }

  buildSpawnPanel(){
    const panel=document.getElementById('spawn-panel');
    panel.innerHTML='';
    const regular=REG.filter(d=>d.s==='p'&&d.a===this.pAge&&!d.special).slice(0,6);
    const special=REG.filter(d=>d.s==='p'&&d.a===this.pAge&&d.special);
    const pu=[...regular,...special];
    pu.forEach((def,i)=>{
      const btn=document.createElement('button');
      btn.className='sbtn'+(def.air?' air':'')+(def.special?' tur':'');
      btn.id='sb_'+def.id;
      btn.innerHTML=`<div class="sn">${def.nm}</div><div class="sc">${def.c}₺</div><div class="sh">♥${def.h}</div><div class="st">${def.air?'✈ Hava':def.special?'🎖 Özel':'⚔ Kara'}</div><div class="skh">TUŞ: ${i+1}</div>`;
      btn.onclick=(e)=>window._spawnUnit(def.id,e.shiftKey);
      btn.title='Shift+Tıkla: 5× gönder';
      if(i<9)this.input.keyboard.on('keydown-'+(i+1),(ev)=>window._spawnUnit(def.id,ev.shiftKey));
      panel.appendChild(btn);
    });
    // Taret satın alma butonu
    const tDef=TURRET_DEFS[this.pAge];
    const tBtn=document.createElement('button');
    tBtn.className='sbtn tur';tBtn.id='btn-buy-tur';
    tBtn.innerHTML=`<div class="sn">🏰 Savunma<br>Kulesi</div><div class="sc">${tDef.cost}₺</div><div class="sh">♥${tDef.hp}</div><div class="st">Max ${MAX_TURRETS}</div>`;
    tBtn.onclick=()=>window._buyTurret&&window._buyTurret();
    panel.appendChild(tBtn);
    this.updateSpawnButtons();
  }

  updateSpawnButtons(){
    const regular=REG.filter(d=>d.s==='p'&&d.a===this.pAge&&!d.special).slice(0,6);
    const special=REG.filter(d=>d.s==='p'&&d.a===this.pAge&&d.special);
    const pu=[...regular,...special];
    pu.forEach(def=>{
      const btn=document.getElementById('sb_'+def.id);
      if(!btn)return;
      this.pGold>=def.c?btn.classList.remove('locked'):btn.classList.add('locked');
    });
    const tBtn=document.getElementById('btn-buy-tur');
    if(tBtn){
      const tDef=TURRET_DEFS[this.pAge];
      const atMax=this.pTurrets.filter(t=>t.alive).length>=MAX_TURRETS;
      (this.pGold>=tDef.cost&&!atMax)?tBtn.classList.remove('locked'):tBtn.classList.add('locked');
      if(atMax){tBtn.querySelector('.sh').textContent='DOLU';}
    }
  }

  buyTurret(){
    if(this.paused||this.gameOver)return;
    const tDef=TURRET_DEFS[this.pAge];
    const alivePTur=this.pTurrets.filter(t=>t.alive).length;
    if(alivePTur>=MAX_TURRETS){showToast('Kule slotu dolu! (Max '+MAX_TURRETS+')','#ffaa44');return;}
    if(this.pGold<tDef.cost){showToast('Yetersiz para!','#ff4444');return;}
    this.pGold-=tDef.cost;
    const usedSlots=this.pTurrets.filter(t=>t.alive).map(t=>t.slotIdx);
    const freeSlot=[0,1,2].find(s=>!usedSlots.includes(s))??alivePTur;
    this.pTurrets.push(this._makeTurret('p',freeSlot));
    this.drawTurretGraphics();
    showToast('🏰 '+tDef.name+' kuruldu!','#44aaff');
    SFX.play('spawn');
    this.updateHUD();
    this.updateSpawnButtons();
  }

  spawnPlayerUnit(id,bulk){
    if(this.paused||this.gameOver)return;
    const def=REG.find(d=>d.id===id);if(!def)return;
    const count=bulk?Math.min(5,Math.floor(this.pGold/def.c)):1;
    if(count<=0){showToast('Yetersiz altın!','#ff4444');return;}
    let spawned=0;
    for(let i=0;i<count;i++){
      const pCount=this.units.filter(u=>u.alive&&u.side==='p').length;
      if(pCount>=MAX_UNITS_PER_SIDE){showToast('Maksimum birim! ('+MAX_UNITS_PER_SIDE+')','#ffaa44');break;}
      if(this.pGold<def.c)break;
      this.pGold-=def.c;
      this.spawnUnit(def,'p',PSX,def.air?AIR_Y:GND-18);
      spawned++;
    }
    if(spawned>0){
      SFX.play('spawn');
      if(spawned>1)showToast('⚡ '+spawned+'× '+def.nm+' gönderildi!','#44ff88');
    }
    this.updateHUD();
  }

  spawnUnit(def,side,x,y){
    const hasPng=PNG_URLS[def.id]&&this.textures.exists('png_'+def.id);
    let key=hasPng?'png_'+def.id:def.id;
    // Fallback: texture yoksa benzer tipe ait bilinen bir doku kullan
    if(!this.textures.exists(key)){
      const fb={
        // Komando özel birimleri
        p_kmd0:'p_g3',p_kmd1:'p_hk33',p_kmd2:'p_mpt',p_kmd3:'p_sar',p_kmd4:'p_cyber',
        // Düşman hava birimleri → en yakın çağ hava birimi
        e_mig21:'e_mig29',e_mi24:'e_ka52',e_su35:'e_mig29',e_stlth:'e_mig29',e_hypers:'e_mig29',
      };
      // Hava birimiyse önce diğer hava birimlerini dene, sonra piyade değil tank
      if(def.air&&side==='e'){
        const airFallbacks=['e_mig29','e_ka52','e_mig21'];
        key=airFallbacks.find(k=>this.textures.exists(k))||'e_ak47';
      }else{
        key=fb[def.id]||(side==='p'?'p_g3':'e_ak47');
      }
    }
    if(!this.textures.exists(key))return;
    // Y dağılımı: hava birimleri sabit, kara birimleri ±10px alan derinliği
    if(!def.air){
      const laneOffset=Phaser.Math.Between(-10,10);
      y=(y||GND-18)+laneOffset;
    }
    const spr=this.add.image(x,y,key);
    spr.setOrigin(.5,.5);spr.setDisplaySize(72,52);
    if(side==='e')spr.setFlipX(true);
    const u={def,side,spr,x,y,hp:def.h,maxHP:def.h,alive:true,target:null,lastAtk:0,crossedLine:false};
    this.units.push(u);return u;
  }

  // ── HEDEF SEÇME (anti-air dahil) ────────────
  findTarget(u){
    const enemies=this.units.filter(e=>e.alive&&e.side!==u.side);
    const inRange=[];
    for(const e of enemies){
      const d=(e.def.air!==u.def.air)?Math.abs(e.x-u.x):Math.sqrt((e.x-u.x)**2+(e.y-u.y)**2);
      if(d<=u.def.r)inRange.push({e,d});
    }
    if(!inRange.length)return null;
    // Hedef önceliği: 'nearest'(varsayılan), 'weakest', 'strongest', 'closest_base'
    const mode=this.targetPriority||'nearest';
    if(mode==='weakest'){inRange.sort((a,b)=>a.e.hp-b.e.hp);}
    else if(mode==='strongest'){inRange.sort((a,b)=>b.e.maxHP-a.e.maxHP);}
    else if(mode==='closest_base'){
      const baseX=u.side==='p'?EBX:PBX;
      inRange.sort((a,b)=>Math.abs(a.e.x-baseX)-Math.abs(b.e.x-baseX));
    }else{inRange.sort((a,b)=>a.d-b.d);}
    return inRange[0].e;
  }

  findTurretTarget(t){
    const enemies=this.units.filter(u=>u.alive&&u.side!==t.side);
    let best=null,bestD=Infinity;
    for(const e of enemies){
      // Taretler her zaman yatay mesafe kullanır (hem kara hem hava)
      const d=Math.abs(e.x-t.x);
      if(d<=t.rng&&d<bestD){bestD=d;best=e;}
    }
    return best;
  }

  tryAttack(u,now){
    if(u._ammoBoost>0)u._ammoBoost-=16;
    const effRt=u._ammoBoost>0?Math.floor(u.def.rt*0.75):u.def.rt;
    if(now-u.lastAtk<effRt)return;
    u.lastAtk=now;
    if(!u.target)return;
    const ux=u.x,uy=u.def.air?u.y:u.y-8;
    const tx=u.target.x,ty=u.target.def.air?u.target.y:u.target.y-8;
    this.spawnMuzzleFlash(ux,u.side==='p'?ux+16:ux-16,uy,u.def.t);
    this.spawnProjectile({x:ux,y:uy,dmg:u.def.d,side:u.side,t:u.def.t,atk:u},u.target,null);
    // Sniper & uzun menzilli → bullet tracer
    if(u.def.r>320||u.def.id&&u.def.id.includes('snp')){
      this.spawnTracer(ux,uy,tx,ty,u.def.id&&u.def.id.includes('snp')?0xff4400:0xffee44);
      // Nişancı: namlu parıltısı
      if(u.def.id&&u.def.id.includes('snp')){
        this.particles.push({x:ux,y:uy,r:8,life:80,maxLife:80,type:'flash',col:0xff8844});
        this.particles.push({x:ux+(u.side==='p'?20:-20),y:uy,r:5,life:120,maxLife:120,type:'spark',vx:(u.side==='p'?40:-40),vy:-15,col:0xff6600});
      }
    }
    SFX.play(u.def.t==='veh'?'cannon':u.def.t==='air'?'cannon':u.def.id&&u.def.id.includes('snp')?'bigboom':'shot');
  }

  tryAttackBase(u,now){
    if(now-u.lastAtk<u.def.rt)return;
    u.lastAtk=now;
    this.spawnMuzzleFlash(u.x,u.side==='p'?u.x+16:u.x-16,u.def.air?u.y:u.y-8,u.def.t);
    const base=u.side==='p'?this.eBase:this.pBase;
    this.spawnProjectile({x:u.x,y:u.def.air?u.y:u.y-8,dmg:u.def.d,side:u.side,t:u.def.t},null,base);
    SFX.play('cannon');
    if(u.side==='e')this.cameras.main.shake(90,0.005);
  }

  tryTurretAttack(t,now){
    if(now-t.lastAtk<t.rt)return;
    const target=this.findTurretTarget(t);
    if(!target){
      // Yavaşça yukarı döndür (bekleme pozisyonu)
      const upAngle=t.side==='p'?-Math.PI*0.35:-Math.PI*0.65;
      t.aimAngle+=(upAngle-t.aimAngle)*0.05;
      return;
    }
    // Hedefe doğru namluyu yönlendir
    // Tahminli nişan — hava/kara için farklı hesap
    const isAir=target.def.air;
    const projSpd=isAir?780:620;
    const rawDist=Math.sqrt((target.x-t.x)**2+(target.y-t.y)**2)||1;
    const travelTime=rawDist/projSpd;
    const dir=target.side==='p'?1:-1;
    const predX=target.x+dir*target.def.sp*travelTime;
    const predY=target.y; // hava birimleri Y'de hareket etmez
    t.aimAngle=Math.atan2(predY-(t.y-7),predX-t.x);
    t.lastAtk=now;
    const mx=t.x+Math.cos(t.aimAngle)*16;
    const my=(t.y-7)+Math.sin(t.aimAngle)*16;
    this.spawnMuzzleFlash(t.x,mx,my,'heavy');
    // Homing bayrağıyla doğrudan push — spawnProjectile kullanmıyoruz
    const spd=isAir?780:620;
    const pdx=predX-mx,pdy=predY-my;
    const pd=Math.sqrt(pdx*pdx+pdy*pdy)||1;
    this.projs.push({x:mx,y:my,vx:(pdx/pd)*spd,vy:(pdy/pd)*spd,
      dmg:t.dmg,side:t.side,tu:target,tb:null,tt:null,dead:false,t:isAir?'air':'heavy',homing:true});
    SFX.play('turret');
  }

  spawnProjectile(from,tu,tb){
    const tx=tu?tu.x:(tb.x);
    const ty=tu?tu.y:GND-40;
    const dx=tx-from.x,dy=ty-from.y,dist=Math.sqrt(dx*dx+dy*dy)||1;
    const spd=500;
    this.projs.push({x:from.x,y:from.y,vx:(dx/dist)*spd,vy:(dy/dist)*spd,dmg:from.dmg,side:from.side,tu,tb,dead:false,t:from.t});
  }

  spawnMuzzleFlash(ux,mx,my,type){
    this.particles.push({x:mx,y:my,life:120,maxLife:120,type:'flash'});
    if(type==='veh'||type==='heavy'){
      for(let i=0;i<4;i++){
        this.particles.push({x:mx,y:my,vx:Phaser.Math.Between(-40,40)/100,vy:Phaser.Math.Between(-70,-15)/100,life:450,maxLife:450,type:'smoke'});
      }
    }
  }

  spawnBlood(x,y){
    for(let i=0;i<6;i++){
      this.particles.push({x,y,vx:Phaser.Math.Between(-80,80)/100,vy:Phaser.Math.Between(-120,-20)/100,life:380,maxLife:380,type:'blood'});
    }
  }

  spawnFloatText(x,y,txt,color){
    const t=this.add.text(x,y,txt,{fontSize:'13px',fontFamily:'Arial',fontStyle:'bold',color,stroke:'#000',strokeThickness:3});
    t.setOrigin(.5,.5);
    this.fxTexts.push({obj:t,vy:-0.7,life:1200,maxLife:1200});
  }

  // ── ANA GÜNCELLEME DÖNGÜSÜ ──────────────────
  update(time,delta){
    if(this.paused||this.gameOver)return;
    const dt=delta*this.gameSpeed;
    this.simTime+=dt;
    this.totalTime+=dt;

    // Bulut animasyonu
    for(const c of this.clouds){
      c.x+=c.speed*(dt/1000);
      if(c.x>W+130)c.x=-160;
    }
    this.cloudGfx.clear();
    this.drawClouds();

    // Cooldown'lar
    if(this.repairCd>0)this.repairCd=Math.max(0,this.repairCd-dt);
    if(this.quizCd>0)this.quizCd=Math.max(0,this.quizCd-dt);
    if(this.airSupCd>0)this.airSupCd=Math.max(0,this.airSupCd-dt);
    if(this.artyCd>0)this.artyCd=Math.max(0,this.artyCd-dt);
    if(this.empCd>0)this.empCd=Math.max(0,this.empCd-dt);
    if(this.mineCd>0)this.mineCd=Math.max(0,this.mineCd-dt);
    if(this.droneCd>0)this.droneCd=Math.max(0,this.droneCd-dt);
    if(this.fogCd>0)this.fogCd=Math.max(0,this.fogCd-dt);
    if(this.logiCd>0)this.logiCd=Math.max(0,this.logiCd-dt);
    if(this.saboCd>0)this.saboCd=Math.max(0,this.saboCd-dt);
    if(this.ironCurtainCd>0)this.ironCurtainCd=Math.max(0,this.ironCurtainCd-dt);
    // Demir Perde süresi
    if(this.ironCurtainActive){
      this.ironCurtainTimer=Math.max(0,this.ironCurtainTimer-dt);
      if(this.ironCurtainTimer<=0){
        this.ironCurtainActive=false;
        const icf=document.getElementById('iron-curtain-flash');
        if(icf)icf.classList.remove('active');
        showToast('🛡 Demir Perde sona erdi','#8888ff');
      }
    }
    // Dinamik gökyüzü — her 5s'de bir yenile
    this._skyTime+=dt;
    if(!this._skyRedrawTimer)this._skyRedrawTimer=0;
    this._skyRedrawTimer+=dt;
    if(this._skyRedrawTimer>=5000){this._skyRedrawTimer=0;this.drawBG();}
    // EMP süresi
    if(this.empActive){
      this.empTimer=Math.max(0,this.empTimer-dt);
      if(this.empTimer<=0){this.empActive=false;this.empSlowedUnits.clear();}
    }
    // Sis aktif
    if(this.fogActive){
      this.fogTimer=Math.max(0,this.fogTimer-dt);
      if(this.fogTimer<=0){this.fogActive=false;const fo=document.getElementById('fog-overlay');if(fo)fo.classList.remove('active');}
    }
    // Sabotaj aktif
    if(this.saboActive){
      this.saboTimer=Math.max(0,this.saboTimer-dt);
      if(this.saboTimer<=0){this.saboActive=false;showToast('💸 Sabotaj sona erdi','#888');}
    }
    // Skor çarpanı timer
    if(this.scoreMultiplier>1){
      this.scoreMultTimer=Math.max(0,this.scoreMultTimer-dt);
      if(this.scoreMultTimer<=0){this.scoreMultiplier=1;const sm=document.getElementById('score-mult');if(sm)sm.classList.remove('show');}
    }
    // Otomatik kayıt (60s)
    this.autoSaveTimer+=dt;
    if(this.autoSaveTimer>=60000){
      this.autoSaveTimer=0;
      try{localStorage.setItem('cgbs_autosave',JSON.stringify({pKills:this.pKills,pGold:Math.floor(this.pGold),pAge:this.pAge,pXP:Math.floor(this.pXP),wave:this.waveCount,time:this.totalTime}));}catch(e){}
    }
    // Dinamik zorluk: oyuncu çok iyi gidiyorsa AI güçlenir
    this.dynDiffTimer+=dt;
    if(this.dynDiffTimer>=30000){
      this.dynDiffTimer=0;
      const killRatio=this.pKills/(Math.max(1,this.eKills));
      if(killRatio>4&&this._dynDiffLevel<3){
        this._dynDiffLevel++;this._aiGoldMult=(this._aiGoldMult||1)*1.15;
        showToast('⚠ AI güçlendi! (Dinamik Zorluk)','#ff8844');
      }
    }
    // Bölge kontrolü
    this._updateZoneControl(dt);
    // Altın sandıkları
    this._updateGoldCrates(dt);
    // Hava durumu döngüsü
    this._updateWeather(dt);
    // Tarihi olaylar
    this.eventTimer+=dt;
    if(this.eventTimer>=this.eventInterval){this.eventTimer=0;this._triggerHistoricalEvent();}
    // Yıldız partikülleri (gece)
    if(this.nightMode)this._drawStars(dt);
    // Altın faizi
    this.interestTimer+=dt;
    if(this.interestTimer>=this.interestInterval){
      this.interestTimer=0;
      if(this.pGold>=800){
        const interest=Math.floor(this.pGold*.03*(this._interestMult||1));
        this.pGold+=interest;
        const it=document.getElementById('interest-toast');
        if(it){it.textContent='💰 Faiz +'+interest+'₺';it.classList.add('pop');setTimeout(()=>it.classList.remove('pop'),1200);}
      }
    }
    // Boss birimi tetikleme
    if(!this.bossSpawnedAges.has(this.eAge)&&this.waveCount>0&&this.waveCount%3===0){
      this.bossSpawnedAges.add(this.eAge);
      this._spawnBoss();
    }
    // Minimap güncelle
    this._updateMinimap();
    if(this.pDefLineCd>0)this.pDefLineCd=Math.max(0,this.pDefLineCd-dt);
    if(this.eDefLineCd>0)this.eDefLineCd=Math.max(0,this.eDefLineCd-dt);
    // Tehlike/panik overlay
    const pBasePct=this.pBase.hp/this.pBase.maxHP;
    const dv=document.getElementById('danger-vignette');
    const po=document.getElementById('panic-overlay');
    if(dv){pBasePct<0.25?dv.classList.add('active'):dv.classList.remove('active');}
    if(po){pBasePct<0.10?po.classList.add('active'):po.classList.remove('active');}
    // Düşük moral uyarısı
    if(this.morale<=15&&!this._moralWarnT){
      this._moralWarnT=true;
      showToast('💔 ORDU ÇÖKÜYOR! Moral kritik seviyede!','#ff2200');
      this.cameras.main.shake(300,0.01,false);
    }
    if(this.morale>20)this._moralWarnT=false;
    // Yağmur sistemi
    this.rainTimer+=dt;
    if(!this.rainActive&&this.rainTimer>=this.rainInterval){
      this.rainActive=true;this.rainTimer=0;
      this.rainDuration=Phaser.Math.Between(12000,25000);
      showToast('🌧 Yağmur başladı — görüş azalıyor','#88aacc');
    }
    if(this.rainActive){
      this.rainDuration-=dt;
      if(this.rainDuration<=0){
        this.rainActive=false;this.rainInterval=Phaser.Math.Between(45000,90000);this.rainTimer=0;
      }else if(Math.random()<0.45){
        const rx=Math.random()*W,ry=Math.random()*40-20;
        this.particles.push({x:rx,y:ry,vx:-18,vy:480+Math.random()*120,life:220+Math.random()*80,maxLife:300,type:'rain'});
      }
    }
    // Müttefik takviye
    this.reinforceTimer+=dt;
    if(this.reinforceTimer>=this.reinforceInterval){
      this.reinforceTimer=0;this.reinforceCount++;
      this._sendReinforcements();
    }
    // Mayın kontrolü
    if(this.mines.length){
      this.mines=this.mines.filter(m=>{
        if(!m.alive)return false;
        const hit=this.units.find(u=>u.alive&&u.side==='e'&&Math.abs(u.x-m.x)<20&&Math.abs(u.y-GND)<30);
        if(hit){
          m.alive=false;
          const dmg=80+this.pAge*40;
          // Alan hasarı
          this.units.filter(u=>u.alive&&u.side==='e'&&Math.abs(u.x-m.x)<55).forEach(u=>this.applyDamage(u,dmg));
          this.particles.push({x:m.x,y:GND-10,r:45,life:550,maxLife:550,type:'exp'});
          this.particles.push({x:m.x,y:GND-10,r:70,life:300,maxLife:300,type:'ring'});
          this.spawnExplosionSparks(m.x,GND-10);
          this.cameras.main.shake(120,0.008);SFX.play('boom');
          return false;
        }
        return true;
      });
    }

    // Ultimate cooldown
    if(this.pUltCd>0)  this.pUltCd  =Math.max(0,this.pUltCd-dt);
    if(this.aiUltCd>0) this.aiUltCd =Math.max(0,this.aiUltCd-dt);
    this.updateUltButton();

    // AI XP → otomatik çağ atlama
    if(this.eAge<4&&this.aiXP>=AGES[this.eAge].xpN){
      this.aiXP-=AGES[this.eAge].xpN;
      this.eAge=Math.min(4,this.eAge+1);
      const nHP=AGES[this.eAge].bHP,oHP=AGES[this.eAge-1].bHP;
      this.eBase.maxHP=nHP;
      this.eBase.hp=Math.min(this.eBase.hp+(nHP-oHP)*.35,nHP);
      this.eTurrets.forEach(t=>{const d=TURRET_DEFS[this.eAge];t.dmg=d.dmg;t.rng=d.rng;t.rt=d.rt;});
      showToast('☠ Düşman '+AGES[this.eAge].name+"'a geçti!",'#ff8844');
      SFX.play('upgrade');
    }

    // Dalga zamanlayıcısı
    this.waveTimer+=dt;
    // Dalga önizlemesi — 5s kala göster
    if(!this._wavePreviewShown&&this.waveTimer>=this.waveInterval-5000){
      this._wavePreviewShown=true;
      this._showWavePreview();
    }
    if(this.waveTimer<this.waveInterval-5000)this._wavePreviewShown=false;
    if(this.waveTimer>=this.waveInterval){
      // Dalga hayatta kalma bonusu
      const killsSinceWave=this.pKills-this.lastWaveKills;
      if(this.waveCount>0&&killsSinceWave>=3){
        const xpBonus=50+this.waveCount*25;const goldBonus=40+this.waveCount*15;
        this.pXP+=xpBonus;this.pGold+=goldBonus;
        this._addLog('🌊 Dalga '+this.waveCount+' savunuldu! +'+xpBonus+' XP +'+goldBonus+'₺');
        showToast('🌊 Dalga Savunuldu! +'+xpBonus+' XP +'+goldBonus+'₺','#44aaff');
      }
      this.lastWaveKills=this.pKills;
      // Dalga öncesi gelir artışı: 10s +%50 bonus
      this.waveIncomeBoost=10000;
      showToast('⚔ Düşman dalgası geliyor! 10s +%50 gelir!','#ffaa00');
      this._showWaveAlert();
      this.waveTimer=0;
      this.waveCount++;
      this._sendAIWave();
    }
    // Dalga gelir boost sayacı
    if(this.waveIncomeBoost>0)this.waveIncomeBoost=Math.max(0,this.waveIncomeBoost-dt);

    // AI ult otomatik tetikleme
    if(this.aiUltCd<=0){
      const threat=this.units.filter(u=>u.alive&&u.side==='p'&&u.x>EBX-420);
      if(threat.length>=4){this.fireUltimate('e');this.aiUltCd=ULT_CD;}
    }

    // Gelir + pasif XP (altınla aynı anda)
    this.incTimer+=dt;
    if(this.incTimer>=this.incInterval){
      this.incTimer=0;
      const inc=AGES[this.pAge].inc;
      const boostMult=this.waveIncomeBoost>0?1.5:1;
      // Yüksek rezerv bonus: 2000₺ üzerinde %10 faiz
      const reserveBonus=this.pGold>=2000?1.1:1;
      // Moral etkisi: yüksek moral gelir artırır
      const moralIncMult=this.morale>=80?1.08:this.morale<=20?0.92:1;
      this.pGold+=inc*boostMult*reserveBonus*moralIncMult;
      // Moral doğal olarak 50'ye yaklaşır
      if(this.morale>50)this.morale=Math.max(50,this.morale-1);
      else if(this.morale<50)this.morale=Math.min(50,this.morale+0.5);
      if(!this.saboActive)this.aiGold+=inc*(this._aiGoldMult||1);
      // Pasif XP
      const xpInc=AGES[this.pAge].xpInc||0;
      if(xpInc>0){
        this.pXP+=xpInc;
        this.aiXP+=AGES[this.eAge].xpInc||0;
        this.spawnFloatText(160,72,'+'+xpInc+' XP','#2ecc71');
      }
    }

    // AI karar
    this.aiTimer+=dt;
    if(this.aiTimer>=this.aiDecTimer){
      this.aiTimer=0;this.aiDecTimer=Phaser.Math.Between(1200,2600)*(this._aiDecMult||1);this.aiDecide();
    }

    // Birim güncellemesi
    for(const u of this.units){
      if(!u.alive)continue;
      if(u.def.km){
        const tx=u.side==='p'?EBX:PBX;
        const dx=tx-u.x,dy=(GND-40)-u.y,dist=Math.sqrt(dx*dx+dy*dy)||1;
        u.x+=(dx/dist)*u.def.sp*(dt/1000);u.y+=(dy/dist)*u.def.sp*(dt/1000);
        u.spr.setPosition(u.x,u.y);
        const base=u.side==='p'?this.eBase:this.pBase;
        if(Math.abs(u.x-tx)<30){
          base.hp=Math.max(0,base.hp-u.def.d);
          this.particles.push({x:u.x,y:u.y,r:38,life:650,maxLife:650,type:'exp'});
          this.spawnBlood(u.x,u.y);this.spawnBlood(u.x,u.y-10);
          if(u.side==='e'){this.pGold+=u.def.rw;this.pXP+=u.def.xp;}else{this.aiGold+=u.def.rw;}
          u.alive=false;
        }
        continue;
      }
      // Savunma hattı geçiş kontrolü
      if(!u.crossedLine){
        if(u.side==='e'&&u.x<=PDEF_LINE&&this.pDefLineCd<=0){
          u.crossedLine=true;
          this._triggerDefenseLine('p');
        }else if(u.side==='p'&&u.x>=EDEF_LINE&&this.eDefLineCd<=0){
          u.crossedLine=true;
          this._triggerDefenseLine('e');
        }
      }
      u.target=this.findTarget(u);
      const baseInRange=this.isNearEnemyBase(u);
      if(u.target){
        this.tryAttack(u,this.simTime);
      }else{
        const nearTurret=this.findNearbyEnemyTurret(u);
        if(nearTurret){
          this.tryAttackTurret(u,nearTurret,this.simTime);
        }else if(baseInRange){
          this.tryAttackBase(u,this.simTime);
        }else{
          const empMult=(this.empActive&&u.side==='e')?0.35:1;
          // Moralsizlik: HP %20 altında birimler %30 yavaşlar
          const moraleMult=(u.hp/u.maxHP<0.2)?0.7:1;
          // Sis altında düşman %15 yavaşlar
          const fogMult=(this.fogActive&&u.side==='e')?0.85:1;
          // Tarihi olay efektleri: fırtına/istihbarat/stun
          if(u._stormSlow>0)u._stormSlow-=dt;
          if(u._intelSlow>0)u._intelSlow-=dt;
          if(u._stunned>0){u._stunned-=dt;u.spr.setPosition(u.x,u.y);continue;}
          const stormMult=(u._stormSlow>0&&u.def.air)?0.7:1;
          const intelMult=(u._intelSlow>0&&u.side==='e')?0.75:1;
          const rainMult=(u._rainSlow>0&&!u.def.air)?0.88:1;
          const sp=u.def.sp*empMult*moraleMult*fogMult*stormMult*intelMult*rainMult;
          // Taktik geri çekilme: HP %15 altında piyade (araç değil) üssüne kaçar
          const hpRatio=u.hp/u.maxHP;
          const retreating=u.side==='p'&&hpRatio<0.15&&u.def.t==='inf'&&!u._retreatDone;
          if(retreating){
            u.x-=sp*0.9*(dt/1000);
            if(u.x<=PBX+60)u._retreatDone=true;
            if(!u._retreatToast){u._retreatToast=true;this.spawnFloatText(u.x,u.y-26,'💨 GERİ!','#ff8833');}
          }else{
            if(u._retreatDone&&hpRatio>0.5)u._retreatDone=false;
            if(u.side==='p')u.x+=sp*(dt/1000);else u.x-=sp*(dt/1000);
          }
        }
      }
      u.spr.setPosition(u.x,u.y);
      // Medikal aura — healer birimler yakındakileri iyileştirir
      if(u.def.healer&&u.alive){
        if(!u._healTimer)u._healTimer=0;
        u._healTimer+=dt;
        if(u._healTimer>=2000){
          u._healTimer=0;
          const healAmt=Math.ceil(u.maxHP*.04);
          this.units.filter(a=>a.alive&&a.side===u.side&&a!==u&&Math.abs(a.x-u.x)<120&&Math.abs(a.y-u.y)<50&&a.hp<a.maxHP).forEach(a=>{
            a.hp=Math.min(a.maxHP,a.hp+healAmt);
            this.spawnFloatText(a.x,a.y-22,'+'+healAmt,'#44ee88');
          });
          // Görsel aura halkası
          this.particles.push({x:u.x,y:u.y,r:120,life:400,maxLife:400,type:'heal_ring'});
        }
      }
    }

    // Taret güncellemesi + otomatik onarım
    for(const t of [...this.pTurrets,...this.eTurrets]){
      if(t.alive){
        this.tryTurretAttack(t,this.simTime);
        // Otomatik onarım: 20s'de bir %5 HP yenilenir
        if(!t._repTimer)t._repTimer=0;
        t._repTimer+=dt;
        if(t._repTimer>=20000&&t.hp<t.maxHP){
          t._repTimer=0;const heal=Math.ceil(t.maxHP*.05);t.hp=Math.min(t.maxHP,t.hp+heal);
        }
      }
    }

    this.updateProjectiles(dt);
    this.updateParticles(dt);
    this.updateFxTexts(dt);
    this._drawUnitShadows();
    if(this.showHPBars)this._drawUnitHPBars();
    this._drawFireEffects(dt);
    this.drawBaseHP();
    this.drawTurretGraphics();
    this.updateHUD();
    this.updateSpawnButtons();
    this._updateAchievCount();

    this.units=this.units.filter(u=>{
      if(!u.alive){u.spr.destroy();return false;}return true;
    });

    // Hasar alan üste alev efekti
    if(this.pBase.hp/this.pBase.maxHP<0.3&&Math.random()<0.35){
      this.particles.push({x:PBX+Phaser.Math.Between(-28,28),y:GND-Phaser.Math.Between(12,55),
        vx:Phaser.Math.Between(-15,15)/100,vy:-Phaser.Math.Between(35,80)/100,
        life:350+Math.random()*300,maxLife:650,type:'fire'});
    }
    if(this.eBase.hp/this.eBase.maxHP<0.3&&Math.random()<0.35){
      this.particles.push({x:EBX+Phaser.Math.Between(-28,28),y:GND-Phaser.Math.Between(12,55),
        vx:Phaser.Math.Between(-15,15)/100,vy:-Phaser.Math.Between(35,80)/100,
        life:350+Math.random()*300,maxLife:650,type:'fire'});
    }
    if(this.eBase.hp<=0){this.endGame(true);return;}
    if(this.pBase.hp<=0){this.endGame(false);return;}
  }

  isNearEnemyBase(u){
    return Math.abs(u.x-(u.side==='p'?EBX:PBX))<(u.def.r+20);
  }

  findNearbyEnemyTurret(u){
    const turrets=u.side==='p'?this.eTurrets:this.pTurrets;
    let best=null,bestD=Infinity;
    for(const t of turrets){
      if(!t.alive)continue;
      const d=Math.abs(u.x-t.x);
      if(d<=u.def.r+15&&d<bestD){bestD=d;best=t;}
    }
    return best;
  }

  tryAttackTurret(u,turret,now){
    if(now-u.lastAtk<u.def.rt)return;
    u.lastAtk=now;
    SFX.play(u.def.t==='veh'?'cannon':'shot');
    this.spawnMuzzleFlash(u.x,u.side==='p'?u.x+16:u.x-16,u.def.air?u.y:u.y-8,u.def.t);
    const fromY=u.def.air?u.y:u.y-8;
    const dx=turret.x-u.x,dy=(turret.y-7)-fromY;
    const dist=Math.sqrt(dx*dx+dy*dy)||1;
    const spd=480;
    this.projs.push({x:u.x,y:fromY,vx:(dx/dist)*spd,vy:(dy/dist)*spd,
      dmg:u.def.d,side:u.side,tu:null,tb:null,tt:turret,dead:false,t:u.def.t});
  }

  updateProjectiles(dt){
    this.projGfx.clear();
    for(const p of this.projs){
      if(p.dead)continue;
      // Homing düzeltmesi — hava birimleri için daha güçlü
      if(p.homing&&p.tu&&p.tu.alive){
        const isAir=p.tu.def.air;
        const hx=p.tu.x-p.x, hy=p.tu.y-p.y;
        const hd=Math.sqrt(hx*hx+hy*hy)||1;
        const spd=isAir?780:620;
        const str=isAir?0.22:0.12;
        const tvx=(hx/hd)*spd, tvy=(hy/hd)*spd;
        p.vx+=(tvx-p.vx)*str;
        p.vy+=(tvy-p.vy)*str;
      }
      p.x+=p.vx*(dt/1000);p.y+=p.vy*(dt/1000);
      // Hava birimi: duman izi
      if(p.t==='air'&&Math.random()<0.5){
        this.particles.push({x:p.x,y:p.y,vx:0,vy:0,life:160+Math.random()*120,maxLife:280,type:'smoke'});
      }
      // Çizim
      if(p.t==='air'){
        this.projGfx.fillStyle(0xffaa00);this.projGfx.fillCircle(p.x,p.y,3.5);
        this.projGfx.lineStyle(1.5,0xff6600,.7);this.projGfx.lineBetween(p.x,p.y,p.x-p.vx*.06,p.y-p.vy*.06);
      }else if(p.t==='veh'||p.t==='heavy'){
        this.projGfx.fillStyle(0xff6600);this.projGfx.fillCircle(p.x,p.y,4);
        this.projGfx.lineStyle(2,0x887766,.4);this.projGfx.lineBetween(p.x,p.y,p.x-p.vx*.07,p.y-p.vy*.07);
      }else{
        this.projGfx.fillStyle(0xffffaa);this.projGfx.fillCircle(p.x,p.y,2);
        this.projGfx.lineStyle(1,0xffff44,.5);this.projGfx.lineBetween(p.x,p.y,p.x-p.vx*.03,p.y-p.vy*.03);
      }
      let hit=false;
      // Kule hedefli mermi (tt = turret target)
      if(!hit&&p.tt&&p.tt.alive){
        const dx=p.x-p.tt.x,dy=p.y-(p.tt.y-7);
        if(Math.sqrt(dx*dx+dy*dy)<18){
          p.tt.hp=Math.max(0,p.tt.hp-p.dmg*0.45);
          this.particles.push({x:p.tt.x,y:p.tt.y-7,r:8,life:200,maxLife:200,type:'exp'});
          if(p.tt.hp<=0){
            p.tt.alive=false;
            this.particles.push({x:p.tt.x,y:p.tt.y,r:22,life:480,maxLife:480,type:'exp'});
            this.spawnExplosionSparks(p.tt.x,p.tt.y-7);
            SFX.play('boom');
            showToast((p.side==='p'?'🏰 Düşman':'🏰 TSK')+' kulesi yıkıldı!',p.side==='p'?'#44ff88':'#ff4444');
          }
          hit=true;
        }
      }
      if(p.tu&&p.tu.alive){
        const dx=p.x-p.tu.x,dy=p.y-p.tu.y;
        const hitR=p.tu.def.air?28:(p.homing?26:18);
        if(Math.sqrt(dx*dx+dy*dy)<hitR){this.applyDamage(p.tu,p.dmg,p.atk||null);hit=true;}
      }else if(p.tb){
        if(Math.abs(p.x-p.tb.x)<65&&Math.abs(p.y-GND)<80){
          p.tb.hp=Math.max(0,p.tb.hp-p.dmg);
          this.spawnBlood(p.x,p.y);
          this.particles.push({x:p.x,y:p.y,r:14,life:320,maxLife:320,type:'exp'});hit=true;
        }
      }
      // Kule isabet kontrolü: oyuncu mermisi → düşman kulesi, düşman mermisi → oyuncu kulesi
      if(!hit){
        const tgtTurrets=p.side==='p'?this.eTurrets:this.pTurrets;
        for(const t of tgtTurrets){
          if(!t.alive)continue;
          if(Math.abs(p.x-t.x)<14&&Math.abs(p.y-(t.y-7))<22){
            t.hp=Math.max(0,t.hp-p.dmg*0.45);
            this.particles.push({x:t.x,y:t.y-7,r:8,life:200,maxLife:200,type:'exp'});
            if(t.hp<=0){
              t.alive=false;
              this.particles.push({x:t.x,y:t.y,r:22,life:480,maxLife:480,type:'exp'});
              this.spawnExplosionSparks(t.x,t.y-7);
              SFX.play('boom');
              showToast((p.side==='p'?'🏰 Düşman':'🏰 TSK')+' kulesi yıkıldı!',p.side==='p'?'#44ff88':'#ff4444');
            }
            hit=true;break;
          }
        }
      }
      if(!hit&&(p.x<0||p.x>W||p.y<-80||p.y>H+50))hit=true;
      if(hit)p.dead=true;
    }
    this.projs=this.projs.filter(p=>!p.dead);
  }

  applyDamage(u,dmg,attacker){
    if(!u.alive)return;
    // Zırh sistemi — araçlar %22 hasar azaltır
    if(u.def.t==='veh')dmg=Math.ceil(dmg*.78);
    // Demir Perde aktifse tüm dost birimler %70 daha az hasar alır
    if(this.ironCurtainActive&&u.side==='p')dmg=Math.ceil(dmg*.3);
    // Nişancı tatbikatı güçlendirmesi
    if(attacker&&attacker._sharpBoost>0&&attacker.def.id&&attacker.def.id.includes('snp')){
      attacker._sharpBoost-=16;dmg=Math.ceil(dmg*2);
    }
    // Moral bonusu: yüksek moral +%20, düşük moral -%15
    if(attacker&&attacker.side==='p'){
      const moraleMod=this.morale>=80?1.2:this.morale<=20?0.85:1;
      if(moraleMod!==1)dmg=Math.ceil(dmg*moraleMod);
    }
    // Reaktif zırh: dost birimler hasar alınca saldırgana %25 geri yansıtır
    if(this._reactiveArmor&&u.side==='p'&&attacker&&attacker.alive){
      const reflect=Math.ceil(dmg*.25);
      attacker.hp=Math.max(0,attacker.hp-reflect);
      if(attacker.hp<=0)attacker.alive=false;
    }
    // Kritik hasar — %15 şans 2× hasar
    let critical=false;
    if(Math.random()<0.15){dmg=Math.ceil(dmg*2);critical=true;}
    u.hp=Math.max(0,u.hp-dmg);
    this.totalDmgDealt+=dmg;
    // Hasar yanıp sönme
    if(u.spr){u.spr.setTint(0xff4444);this.time.delayedCall(120,()=>{if(u.alive&&u.spr)u.spr.clearTint();});}
    this.spawnBlood(u.x,u.y-8);
    if(critical)this.spawnFloatText(u.x,u.y-30,'💥KRİTİK! -'+Math.round(dmg),'#ff2200');
    else this.spawnFloatText(u.x,u.y-30,'-'+Math.round(dmg),'#ff4444');
    if(u.hp<=0){
      u.alive=false;
      const bigUnit=u.def.t==='veh'||u.def.special;
      const expR=bigUnit?34:22;
      this.particles.push({x:u.x,y:u.y,r:expR,life:420,maxLife:420,type:'exp'});
      this.particles.push({x:u.x,y:u.y,r:expR*1.4,life:260,maxLife:260,type:'ring'});
      if(bigUnit){
        this.particles.push({x:u.x,y:u.y,r:expR*.6,life:280,maxLife:280,type:'exp'});
        this.cameras.main.shake(70,0.004);
      }
      this.spawnExplosionSparks(u.x,u.y);
      if(!u.def.air)this.spawnCrater(u.x);
      SFX.play(bigUnit?'boom':'boom');
      if(u.side==='e'){
        const lootMult=this._lootBonus||1;
        this.pGold+=(u.def.rw*lootMult);this.pXP+=u.def.xp;
        this.aiXP+=Math.floor(u.def.xp*.3);
        this.pKills++;
        this._checkKillCelebrate();
        this._registerComboKill();
        this.morale=Math.min(100,this.morale+2);
        // Deneyim/veteran sistemi
        if(attacker&&attacker.side==='p'){
          if(!attacker.killCount)attacker.killCount=0;
          attacker.killCount++;
          if(attacker.killCount===5&&!attacker.veteran){
            attacker.veteran=true;
            attacker.def={...attacker.def,d:Math.ceil(attacker.def.d*1.2),rt:Math.floor(attacker.def.rt*.9)};
            this.spawnFloatText(attacker.x,attacker.y-36,'⭐ VETERAN!','#ffdd00');
            if(attacker.spr)attacker.spr.setTint(0xffee44);
            this.time.delayedCall(800,()=>{if(attacker.alive&&attacker.spr)attacker.spr.clearTint();});
          }
        }
        this.spawnFloatText(u.x,u.y-18,'+'+u.def.rw+'₺','#44ff88');
        // Kill feed
        const killerName=attacker?attacker.def.nm:'TSK';
        this._addKillFeed(killerName,u.def.nm,'p');
        // Kill streak
        if(this.simTime-this.lastKillTime<4000){
          this.streakCount++;
          if(this.streakCount>=3){
            const bonus=Math.min(this.streakCount,10)*22;
            this.pGold+=bonus;
            const sd=document.getElementById('streak-display');
            if(sd){sd.textContent='🔥 '+this.streakCount+'x SERİ! +'+bonus+'₺';sd.classList.add('visible');clearTimeout(this._streakTO);this._streakTO=setTimeout(()=>{sd.classList.remove('visible');},1800);}
          }
        }else{this.streakCount=1;}
        this.lastKillTime=this.simTime;
        // Saldıran birimin XP (birim deneyimi)
        if(attacker&&attacker.alive){
          attacker.kills=(attacker.kills||0)+1;
          if(attacker.kills===3){
            attacker.def={...attacker.def,d:Math.ceil(attacker.def.d*1.2),sp:attacker.def.sp*1.1};
            this.spawnFloatText(attacker.x,attacker.y-20,'⭐ UZMAN','#f39c12');
          }else if(attacker.kills===7){
            attacker.def={...attacker.def,d:Math.ceil(attacker.def.d*1.15)};
            this.spawnFloatText(attacker.x,attacker.y-20,'⭐⭐ VETERİN','#f39c12');
          }
        }
        // Başarım kontrolü
        this._checkAchievements();
      }else{
        this.aiGold+=u.def.rw;
        this.aiXP+=u.def.xp;
        this.eKills++;
        this.morale=Math.max(0,this.morale-3);
        this.spawnFloatText(u.x,u.y-18,'+'+u.def.rw+'₺','#ff9944');
        this._addKillFeed('Düşman',u.def.nm,'e');
      }
      this.spawnBlood(u.x,u.y);this.spawnBlood(u.x+5,u.y-5);
    }
  }

  updateParticles(dt){
    this.fxGfx.clear();
    for(const p of this.particles){
      p.life-=dt;if(p.life<=0)continue;
      const a=p.life/p.maxLife;
      if(p.type==='flash'){
        this.fxGfx.fillStyle(0xffee44,a);this.fxGfx.fillCircle(p.x,p.y,7*(1-a*.5));
        this.fxGfx.fillStyle(0xffffff,a*.7);this.fxGfx.fillCircle(p.x,p.y,3);
      }else if(p.type==='blood'){
        p.x+=p.vx*dt*.1;p.y+=p.vy*dt*.1;p.vy+=0.022;
        this.fxGfx.fillStyle(0xcc1111,a);this.fxGfx.fillCircle(p.x,p.y,3);
      }else if(p.type==='smoke'){
        p.x+=p.vx*dt*.05;p.y+=p.vy*dt*.05;
        const r=4+8*(1-a);
        this.fxGfx.fillStyle(0x887766,a*.55);this.fxGfx.fillCircle(p.x,p.y,r);
      }else if(p.type==='exp'){
        const r=p.r*(2-a);
        this.fxGfx.fillStyle(0xff8800,a);this.fxGfx.fillCircle(p.x,p.y,r);
        this.fxGfx.fillStyle(0xffee22,a*.75);this.fxGfx.fillCircle(p.x,p.y,r*.55);
        this.fxGfx.fillStyle(0xffffff,a*.3);this.fxGfx.fillCircle(p.x,p.y,r*.25);
      }else if(p.type==='spark'){
        p.x+=p.vx*dt*.1;p.y+=p.vy*dt*.1;p.vy+=0.018;
        const col=a>0.6?0xffee22:a>0.3?0xff8800:0xff2200;
        this.fxGfx.fillStyle(col,a);
        this.fxGfx.fillCircle(p.x,p.y,2);
      }else if(p.type==='fire'){
        p.x+=p.vx*dt*.1;p.y+=p.vy*dt*.1;p.vy-=0.006;
        const r=3+4*(1-a);
        const col=a>0.7?0xffee00:a>0.45?0xff8800:0xff2200;
        this.fxGfx.fillStyle(col,a*.85);this.fxGfx.fillCircle(p.x,p.y,r);
        this.fxGfx.fillStyle(0xffffff,a*.2);this.fxGfx.fillCircle(p.x,p.y,r*.4);
      }else if(p.type==='ring'){
        const rr=p.r*(1.2-a*.2);
        const alpha=a*.65;
        this.fxGfx.lineStyle(3*a,0xff8800,alpha);this.fxGfx.strokeCircle(p.x,p.y,rr);
        this.fxGfx.lineStyle(1.5*a,0xffff44,alpha*.5);this.fxGfx.strokeCircle(p.x,p.y,rr*.65);
      }else if(p.type==='rain'){
        p.x+=p.vx*(dt/1000);p.y+=p.vy*(dt/1000);
        this.fxGfx.lineStyle(1,0x88aacc,a*.5);
        this.fxGfx.lineBetween(p.x,p.y,p.x+2,p.y+10);
      }else if(p.type==='heal_ring'){
        const rr=p.r*(0.5+0.5*(1-a));
        this.fxGfx.lineStyle(2*a,0x44ee88,a*.55);this.fxGfx.strokeCircle(p.x,p.y,rr);
        this.fxGfx.lineStyle(1*a,0xaaffcc,a*.3);this.fxGfx.strokeCircle(p.x,p.y,rr*.6);
      }else if(p.type==='tracer'){
        // Bullet tracer: kısa parlak çizgi
        p.x+=p.vx*(dt/1000);p.y+=p.vy*(dt/1000);
        this.fxGfx.lineStyle(2,p.col||0xffee88,a*.85);
        this.fxGfx.lineBetween(p.x,p.y,p.x-p.vx*.06,p.y-p.vy*.06);
        this.fxGfx.lineStyle(1,0xffffff,a*.4);
        this.fxGfx.lineBetween(p.x,p.y,p.x-p.vx*.03,p.y-p.vy*.03);
      }else if(p.type==='scorch'){
        // Yanık zemin — zemin seviyesinde kalıcı daire
        this.fxGfx.fillStyle(0x111111,a*.35);this.fxGfx.fillEllipse(p.x,GND+2,p.r*2,p.r*.6);
        this.fxGfx.lineStyle(1,0x333311,a*.2);this.fxGfx.strokeEllipse(p.x,GND+2,p.r*2,p.r*.6);
      }else if(p.type==='lightning'){
        // Şimşek efekti — zigzag çizgi
        if(!p.segs){p.segs=[];let lx=p.x,ly=p.y;for(let i=0;i<6;i++){const nx=lx+(p.tx-p.x)/6+Phaser.Math.Between(-20,20);const ny=ly+(p.ty-p.y)/6;p.segs.push([lx,ly,nx,ny]);lx=nx;ly=ny;}p.segs.push([lx,ly,p.tx,p.ty]);}
        p.segs.forEach(([x1,y1,x2,y2])=>{
          this.fxGfx.lineStyle(3*a,0xaaccff,a*.9);this.fxGfx.lineBetween(x1,y1,x2,y2);
          this.fxGfx.lineStyle(1*a,0xffffff,a*.6);this.fxGfx.lineBetween(x1,y1,x2,y2);
        });
      }
    }
    this.particles=this.particles.filter(p=>p.life>0);
  }

  updateFxTexts(dt){
    for(const f of this.fxTexts){
      f.life-=dt;f.obj.y+=f.vy*(dt*.1);f.obj.setAlpha(f.life/f.maxLife);
      if(f.life<=0)f.obj.destroy();
    }
    this.fxTexts=this.fxTexts.filter(f=>f.life>0);
  }

  drawBaseHP(){
    const g=this.hpGfx;g.clear();
    const bw=100;
    [[this.pBase,PBX],[this.eBase,EBX]].forEach(([base,bx])=>{
      const pct=base.hp/base.maxHP;
      g.fillStyle(0x222);g.fillRect(bx-50,GND-108,bw,8);
      g.fillStyle(pct>0.5?0x22cc44:pct>0.25?0xffaa22:0xff3322);
      g.fillRect(bx-50,GND-108,bw*pct,8);
      g.lineStyle(1,0x888,1);g.strokeRect(bx-50,GND-108,bw,8);
    });
    for(const u of this.units){
      if(!u.alive)continue;
      const pct=u.hp/u.maxHP;
      const bx=u.x-16,by=u.y-(u.def.air?18:30);
      g.fillStyle(0x111);g.fillRect(bx,by,32,4);
      g.fillStyle(pct>0.5?0x22cc44:pct>0.25?0xffaa22:0xff3322);
      g.fillRect(bx,by,32*pct,4);
      // Komando altın halka + veteran yıldızı
      if(u.def.special){g.lineStyle(2,0xf39c12,.85);g.strokeCircle(u.x,u.y,28);}
      if(u.kills>=3){g.lineStyle(1,0xffee00,.7);g.strokeCircle(u.x,u.y,u.kills>=7?32:30);}
    }
  }

  aiDecide(){
    // Taret alımı birim limitinden BAĞIMSIZ — önce kontrol et
    const eTurDef=TURRET_DEFS[this.eAge];
    const aliveTurrets=this.eTurrets.filter(t=>t.alive).length;
    if(Math.random()<0.25&&aliveTurrets<MAX_TURRETS&&this.aiGold>=eTurDef.cost){
      this.aiGold-=eTurDef.cost;
      // Boş slot bul
      const usedSlots=this.eTurrets.filter(t=>t.alive).map(t=>t.slotIdx);
      const freeSlot=[0,1,2].find(s=>!usedSlots.includes(s))??0;
      this.eTurrets.push(this._makeTurret('e',freeSlot));
      showToast('☠ Düşman savunma kulesi kurdu!','#ff8844');
      return;
    }

    const eCount=this.units.filter(u=>u.alive&&u.side==='e').length;
    if(eCount>=MAX_UNITS_PER_SIDE)return;

    // Oyuncunun hava birimlerini say — hava varsa karşı-birim önceliklendir
    const pAirCount=this.units.filter(u=>u.alive&&u.side==='p'&&u.def.air).length;
    let pool=REG.filter(d=>d.s==='e'&&d.a===this.eAge);
    if(pAirCount>=2&&Math.random()<0.55){
      // Hava karşı: hava birimi veya MANPADS benzeri
      const airPool=pool.filter(d=>d.air||d.t==='inf');
      if(airPool.length)pool=airPool;
    }
    let affordable=pool.filter(d=>d.c<=this.aiGold);
    if(!affordable.length){
      affordable=REG.filter(d=>d.s==='e'&&d.a<this.eAge&&d.c<=this.aiGold);
    }
    if(!affordable.length)return;

    // %65 en pahalı, %35 rastgele
    const chosen=Math.random()<0.65
      ?affordable.sort((a,b)=>b.c-a.c)[0]
      :affordable[Math.floor(Math.random()*affordable.length)];
    this.aiGold-=chosen.c;
    this.spawnUnit(chosen,'e',ESX,chosen.air?AIR_Y:GND-18);
  }

  // ── ULTIMATE ABILITY ────────────────────────
  playerFireUlt(){
    if(this.paused||this.gameOver)return;
    if(this.pUltCd>0){showToast('Özel Güç hazır değil! '+(Math.ceil(this.pUltCd/1000))+'s','#ff8844');return;}
    const k=this.fireUltimate('p');
    if(k===0){showToast('Hedef bulunamadı!','#ffaa44');return;}
    this.pUltCd=ULT_CD;
  }

  fireUltimate(side){
    const targets=this.units.filter(u=>u.alive&&u.side!==side);
    if(!targets.length)return 0;
    const killCount=Math.ceil(targets.length*.5);
    const shuffled=[...targets];
    for(let i=shuffled.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[shuffled[i],shuffled[j]]=[shuffled[j],shuffled[i]];}
    shuffled.slice(0,killCount).forEach(u=>{
      this.particles.push({x:u.x,y:u.y,r:30,life:580,maxLife:580,type:'exp'});
      this.particles.push({x:u.x,y:u.y,r:14,life:360,maxLife:360,type:'exp'});
      this.spawnBlood(u.x,u.y);this.spawnBlood(u.x+Phaser.Math.Between(-10,10),u.y-5);
      if(u.side==='e'){this.pGold+=u.def.rw;this.pXP+=u.def.xp;this.spawnFloatText(u.x,u.y-20,'+'+u.def.rw+'₺','#44ff88');}
      else{this.aiGold+=u.def.rw;this.spawnFloatText(u.x,u.y-20,'+'+u.def.rw+'₺','#ff9944');}
      u.alive=false;
    });
    this.cameras.main.flash(380,255,60,30,false);
    this.cameras.main.shake(320,0.014);
    SFX.play('ult');
    showToast((side==='p'?'⚡ TSK Özel Gücü':'☠ Düşman Özel Gücü')+': '+killCount+' birim imha!',side==='p'?'#ff4422':'#ff8822');
    return killCount;
  }

  updateUltButton(){
    const btn=document.getElementById('btnUlt');
    const txt=document.getElementById('ultTxt');
    const bar=document.getElementById('ultBar');
    const dot=document.getElementById('ai-ult-dot');
    if(!btn)return;
    if(this.pUltCd<=0){
      btn.disabled=false;btn.classList.add('ready');
      if(txt)txt.textContent='HAZİR!';if(bar)bar.style.width='100%';
    }else{
      btn.disabled=true;btn.classList.remove('ready');
      if(txt)txt.textContent=Math.ceil(this.pUltCd/1000)+'s';
      if(bar)bar.style.width=((ULT_CD-this.pUltCd)/ULT_CD*100).toFixed(1)+'%';
    }
    if(dot){this.aiUltCd<=0?dot.classList.add('ready'):dot.classList.remove('ready');}
  }

  // ── ÇAĞ ATLAMA ──────────────────────────────
  tryUpgrade(){
    if(this.pAge>=4)return;
    const need=AGES[this.pAge].xpN;
    if(this.pXP<need){showToast('Yeterli XP yok! Gerekli: '+need+' XP','#ffaa44');return;}
    this.pXP-=need;this.pAge++;
    const nHP=AGES[this.pAge].bHP,oHP=AGES[this.pAge-1].bHP;
    this.pBase.maxHP=nHP;this.pBase.hp=Math.min(this.pBase.hp+(nHP-oHP)*.4,nHP);
    this.eAge=this.pAge;
    const enHP=AGES[this.eAge].bHP,eoHP=AGES[Math.max(0,this.eAge-1)].bHP;
    this.eBase.maxHP=enHP;this.eBase.hp=Math.min(this.eBase.hp+(enHP-eoHP)*.4,enHP);
    // Taretleri yeni çağa güncelle
    const upd=(arr,side)=>arr.forEach((t,i)=>{const d=TURRET_DEFS[this.pAge];t.dmg=d.dmg;t.rng=d.rng;t.rt=d.rt;});
    upd(this.pTurrets,'p');upd(this.eTurrets,'e');
    document.getElementById('age-yr').textContent=AGES[this.pAge].year;
    document.getElementById('age-nm').textContent=AGES[this.pAge].name;
    document.getElementById('upgTxt').textContent=this.pAge<4?AGES[this.pAge].xpN+' XP':'MAX';
    this.buildSpawnPanel();
    showToast('⬆ '+AGES[this.pAge].name+"'a geçildi!",'#44ff88');
    this._showAgeTransition(AGES[this.pAge].name);
    this._addLog('⬆ '+AGES[this.pAge].name+' çağına geçildi');
    SFX.play('upgrade');
    this.cameras.main.flash(400,80,220,80,false);
    this.updateHUD();this.baseDirty=true;this.drawBases();
  }

  // ── BİLGİ YARIŞMASI ─────────────────────────
  openQuiz(){
    if(this.paused)return;
    if(this.quizCd>0){showToast('Bilgi sorusu hazır değil! '+Math.ceil(this.quizCd/1000)+'s','#aa44ff');return;}
    this.paused=true;
    const q=QUIZ[Phaser.Math.Between(0,QUIZ.length-1)];
    this._quiz=q;
    document.getElementById('qqt').textContent=q.q;
    document.getElementById('qrh').textContent='✓ Doğru cevap: +'+q.r+'₺';
    document.getElementById('qres').textContent='';
    document.getElementById('qcb').classList.remove('show');
    const ow=document.getElementById('qow');ow.innerHTML='';
    ['A','B','C','D'].forEach((l,i)=>{
      const b=document.createElement('button');b.className='qopt';
      b.innerHTML=`<span class="qltr">${l}</span>${q.opts[i]}`;
      b.onclick=()=>this.answerQuiz(i);ow.appendChild(b);
    });
    document.getElementById('qmodal').classList.add('open');
    // Geri sayım başlat
    clearInterval(this._quizTimer);
    this._quizTimeLeft=20;
    const tel=document.getElementById('qtimer');
    if(tel){tel.textContent=20;tel.className='';}
    this._quizTimer=setInterval(()=>{
      this._quizTimeLeft--;
      if(tel){
        tel.textContent=this._quizTimeLeft;
        if(this._quizTimeLeft<=5)tel.classList.add('urgent');
      }
      if(this._quizTimeLeft<=0){
        clearInterval(this._quizTimer);this._quizTimer=null;
        if(tel){tel.classList.remove('urgent');tel.classList.add('expired');}
        document.querySelectorAll('.qopt').forEach(o=>{o.classList.add('answered');o.onclick=null;});
        document.querySelectorAll('.qopt')[q.ans].classList.add('correct');
        document.getElementById('qres').innerHTML='<span style="color:#f88">⏰ Süre doldu! Doğru: '+q.opts[q.ans]+'</span>';
        SFX.play('quiz_fail');
        document.getElementById('qcb').classList.add('show');
      }
    },1000);
  }

  answerQuiz(idx){
    clearInterval(this._quizTimer);this._quizTimer=null;
    const tel=document.getElementById('qtimer');
    if(tel){tel.classList.remove('urgent');tel.classList.add('expired');}
    const q=this._quiz;
    document.querySelectorAll('.qopt').forEach(o=>{o.classList.add('answered');o.onclick=null;});
    if(idx===q.ans){
      document.querySelectorAll('.qopt')[idx].classList.add('correct');
      document.getElementById('qres').innerHTML='<span style="color:#7dffac">✓ Doğru! +'+q.r+'₺</span>';
      this.pGold+=q.r;
      SFX.play('quiz_ok');
    }else{
      document.querySelectorAll('.qopt')[idx].classList.add('wrong');
      document.querySelectorAll('.qopt')[q.ans].classList.add('correct');
      document.getElementById('qres').innerHTML='<span style="color:#f88">✗ Yanlış. Doğru: '+q.opts[q.ans]+'</span>';
      SFX.play('quiz_fail');
    }
    document.getElementById('qcb').classList.add('show');
  }

  closeQuiz(){
    clearInterval(this._quizTimer);this._quizTimer=null;
    document.getElementById('qmodal').classList.remove('open');
    this.quizCd=this.quizCdMax;
    this.paused=false;this.updateHUD();
  }

  updateHUD(){
    const pb=this.pBase,eb=this.eBase;
    document.getElementById('fphp').style.width=(pb.hp/pb.maxHP*100)+'%';
    document.getElementById('fehp').style.width=(eb.hp/eb.maxHP*100)+'%';
    document.getElementById('vphp').textContent=Math.ceil(pb.hp)+'/'+pb.maxHP;
    document.getElementById('vehp').textContent=Math.ceil(eb.hp)+'/'+eb.maxHP;
    document.getElementById('vgold').textContent=Math.floor(this.pGold)+'₺';
    const xn=this.pAge<4?AGES[this.pAge].xpN:AGES[4].xpN;
    document.getElementById('fxp').style.width=Math.min(100,this.pXP/xn*100)+'%';
    document.getElementById('vxp').textContent=Math.floor(this.pXP)+'/'+xn;
    document.getElementById('vaig').textContent=Math.floor(this.aiGold)+'₺';
    const up=document.getElementById('btnUpg');
    if(this.pAge>=4){up.style.opacity='.4';up.disabled=true;up.classList.remove('glow');}
    else{
      const ready=this.pXP>=AGES[this.pAge].xpN;
      up.style.opacity=ready?'1':'.65';up.disabled=false;
      ready?up.classList.add('glow'):up.classList.remove('glow');
    }
    document.getElementById('upgTxt').textContent=this.pAge<4?AGES[this.pAge].xpN+' XP':'MAX ÇAĞ';
    // Dalga sayacı + güç tahmini
    const vwave=document.getElementById('vwave');
    if(vwave){
      const wl=Math.max(0,Math.ceil((this.waveInterval-this.waveTimer)/1000));
      const wavePower=this.waveCount<3?'🟢Hafif':this.waveCount<6?'🟡Orta':this.waveCount<10?'🔴Ağır':'☢Ezici';
      vwave.textContent=wl>0?wl+'s ('+wavePower+')':'Geliyor!';
      vwave.style.color=wl<=5?'#ff3300':'#ff8844';
    }
    // Kill sayacı
    const vk=document.getElementById('vkills');const vek=document.getElementById('vekills');
    if(vk)vk.textContent=this.pKills;if(vek)vek.textContent=this.eKills;
    // Gelir göstergesi
    const vi=document.getElementById('vinc');
    if(vi)vi.textContent='+'+AGES[this.pAge].inc+'₺/3s';
    // Onarma butonu
    const rb=document.getElementById('btnRep');const rt=document.getElementById('repTxt');
    if(rb){
      const cost=200+this.pAge*100;
      const onCd=this.repairCd>0;
      const canAff=this.pGold>=cost;
      const full=this.pBase.hp>=this.pBase.maxHP;
      rb.disabled=onCd||!canAff||full;
      rb.style.opacity=(onCd||!canAff||full)?'0.45':'1';
      if(rt){if(onCd)rt.textContent=Math.ceil(this.repairCd/1000)+'s';
             else if(full)rt.textContent='Üs tam sağlıkta';
             else rt.textContent=cost+'₺ / +%15';}
    }
    // Bilgi sorusu butonu
    const qb=document.getElementById('btnQuiz');const qt=document.getElementById('quizTxt');
    if(qb){
      const onCd=this.quizCd>0;
      qb.disabled=onCd;
      if(qt)qt.textContent=onCd?'Bekleniyor: '+Math.ceil(this.quizCd/1000)+'s':'';
    }
    // Birim sayacı
    const pAlive=this.units.filter(u=>u.alive&&u.side==='p').length;
    const eAlive=this.units.filter(u=>u.alive&&u.side==='e').length;
    const vpu=document.getElementById('vpunits');const veu=document.getElementById('veunits');
    if(vpu)vpu.textContent=pAlive+'/'+MAX_UNITS_PER_SIDE;
    if(veu)veu.textContent=eAlive+'/'+MAX_UNITS_PER_SIDE;
    // Cephe çizgisi göstergesi
    const allAlive=this.units.filter(u=>u.alive);
    if(allAlive.length){
      const midX=allAlive.reduce((s,u)=>s+u.x,0)/allAlive.length;
      const pct=Math.max(0.05,Math.min(0.95,(midX-PBX)/(EBX-PBX)));
      const fp=document.getElementById('frontline-p');const fe=document.getElementById('frontline-e');const fm=document.getElementById('frontline-mid');
      if(fp)fp.style.width=(pct*100)+'%';
      if(fe)fe.style.width=((1-pct)*100)+'%';
      if(fm)fm.style.left=(pct*100)+'%';
    }
    // Hava desteği butonu
    const asb=document.getElementById('btnAirSup');const ast=document.getElementById('airSupTxt');
    if(asb){
      const cost=600+this.pAge*200;const onCd=this.airSupCd>0;
      asb.disabled=onCd||this.pGold<cost;
      if(ast)ast.textContent=onCd?Math.ceil(this.airSupCd/1000)+'s':cost+'₺';
    }
    // Mayın butonu
    const mnb=document.getElementById('btnMine');const mnt=document.getElementById('mineTxt');
    if(mnb){
      const onCd=this.mineCd>0;
      mnb.disabled=onCd||this.pGold<150;
      if(mnt)mnt.textContent=onCd?Math.ceil(this.mineCd/1000)+'s':'150₺';
    }
    // Hız butonu rengi
    const sb=document.getElementById('btnSpd');
    if(sb)this.gameSpeed===2?sb.classList.add('fast'):sb.classList.remove('fast');
    // EMP butonu
    const empb=document.getElementById('btnEmp');const empt=document.getElementById('empTxt');
    if(empb){
      const onCd=this.empCd>0;
      empb.disabled=onCd||this.pGold<500;
      empb.style.opacity=(onCd||this.pGold<500)?'0.45':'1';
      if(empt)empt.textContent=onCd?Math.ceil(this.empCd/1000)+'s':'500₺';
    }
    // Ağır Topçu butonu
    const artb=document.getElementById('btnArty');const artt=document.getElementById('artyTxt');
    if(artb){
      const cost=1200+this.pAge*300;const onCd=this.artyCd>0;
      artb.disabled=onCd||this.pGold<cost;
      artb.style.opacity=(onCd||this.pGold<cost)?'0.45':'1';
      if(artt)artt.textContent=onCd?Math.ceil(this.artyCd/1000)+'s':cost+'₺';
    }
    // Gece modu butonu
    const nib=document.getElementById('btnNight');
    if(nib)nib.textContent=this.nightMode?'☀ Gündüz':'🌙 Gece Modu';
    // Drone butonu
    const drb=document.getElementById('btnDrone');const drt=document.getElementById('droneTxt');
    if(drb){const onCd=this.droneCd>0;drb.disabled=onCd||this.pGold<700;drb.style.opacity=(onCd||this.pGold<700)?'0.45':'1';if(drt)drt.textContent=onCd?Math.ceil(this.droneCd/1000)+'s':'700₺';}
    // Sis butonu
    const fgb=document.getElementById('btnFog');const fgt=document.getElementById('fogTxt');
    if(fgb){const onCd=this.fogCd>0;fgb.disabled=onCd||this.pGold<400;fgb.style.opacity=(onCd||this.pGold<400)?'0.45':'1';if(fgt)fgt.textContent=onCd?Math.ceil(this.fogCd/1000)+'s':'400₺';}
    // Nükleer butonu
    const nkb=document.getElementById('btnNuke');const nkt=document.getElementById('nukeTxt');
    if(nkb){nkb.disabled=this.nukeUsed||this.pGold<5000;nkb.style.opacity=(this.nukeUsed||this.pGold<5000)?'0.45':'1';if(nkt)nkt.textContent=this.nukeUsed?'KULLANILDI':'5000₺';}
    // Lojistik butonu
    const lgb=document.getElementById('btnLogi');const lgt=document.getElementById('logiTxt');
    if(lgb){const onCd=this.logiCd>0;lgb.disabled=onCd||this.pGold<500;lgb.style.opacity=(onCd||this.pGold<500)?'0.45':'1';if(lgt)lgt.textContent=onCd?Math.ceil(this.logiCd/1000)+'s':'500₺';}
    // Sabotaj butonu
    const sbb=document.getElementById('btnSabo');const sbt=document.getElementById('saboTxt');
    if(sbb){const onCd=this.saboCd>0;sbb.disabled=onCd||this.pGold<800;sbb.style.opacity=(onCd||this.pGold<800)?'0.45':'1';if(sbt)sbt.textContent=onCd?Math.ceil(this.saboCd/1000)+'s':'800₺';}
    // Moral göstergesi + bar
    const vm=document.getElementById('vmorale');
    if(vm){
      const m=Math.round(this.morale);
      vm.textContent=m;
      vm.style.color=m>=80?'#44ee88':m>=50?'#f5a623':m>=25?'#ff8844':'#ff3333';
    }
    const mbf=document.getElementById('morale-bar-fill');
    if(mbf){
      const m=this.morale;
      mbf.style.width=m+'%';
      mbf.style.background=m>=80?'linear-gradient(90deg,#27ae60,#44ee88)':m>=50?'linear-gradient(90deg,#e67e22,#f5a623)':m>=25?'linear-gradient(90deg,#c0392b,#ff8844)':'linear-gradient(90deg,#900,#f00)';
    }
    // Hedef öncelik butonu güncelle
    const tgt=document.getElementById('targetTxt');
    if(tgt){const tl={nearest:'EN YAKIN',weakest:'EN ZAYIF',strongest:'EN GÜÇLÜ',closest_base:'EN İLERİ'};tgt.textContent=tl[this.targetPriority||'nearest'];}
    // Hava durumu göstergesi
    const wi=document.getElementById('weather-indicator');
    if(wi){const wIcons={clear:'☀ Açık',rain:'🌧 Yağmur',cloudy:'⛅ Bulutlu'};wi.textContent=wIcons[this.curWeather||'clear']||'☀ Açık';}
    // Demir Perde butonu
    const icb=document.getElementById('btnIronCurtain');const ict=document.getElementById('icTxt');
    if(icb){
      const onCd=this.ironCurtainCd>0;const active=this.ironCurtainActive;
      icb.disabled=onCd&&!active;icb.style.opacity=(onCd&&!active)?'0.45':'1';
      icb.classList.toggle('ready',!onCd&&!active);
      if(ict)ict.textContent=active?Math.ceil(this.ironCurtainTimer/1000)+'s AKTİF':onCd?Math.ceil(this.ironCurtainCd/1000)+'s':'HAZIR';
    }
    // Kule yükseltme butonu
    const tub=document.getElementById('turret-upgrade-btn');
    if(tub){
      const maxLvl=3;const tlvl=this.turretUpgLevel||0;
      const tcost=600+tlvl*400;
      const stars=['','⭐','⭐⭐','⭐⭐⭐'];
      if(tlvl>=maxLvl){tub.textContent='🏰 Kule MAX '+stars[maxLvl];tub.disabled=true;tub.style.opacity='0.5';}
      else{tub.textContent='🔧 Kule Yükselt '+stars[tlvl]+' → '+stars[tlvl+1]+' ('+tcost+'₺)';const canAff=this.pGold>=tcost;tub.disabled=!canAff;tub.style.opacity=canAff?'1':'0.45';}
    }
    // Skor çarpanı gösterge
    const sm=document.getElementById('score-mult');
    if(sm&&this.scoreMultiplier>1){sm.textContent='⚡ ×'+this.scoreMultiplier+' Skor! ('+Math.ceil(this.scoreMultTimer/1000)+'s)';sm.classList.add('show');}
    // Aktif durum göstergesi EMP/Sis/Sabotaj
    const empRow=document.getElementById('emp-status-row');const vEmpT=document.getElementById('vEmpTimer');
    if(empRow){empRow.style.display=this.empActive?'flex':'none';if(vEmpT&&this.empActive)vEmpT.textContent=Math.ceil(this.empTimer/1000)+'s';}
    const fogRow=document.getElementById('fog-status-row');const vFogT=document.getElementById('vFogTimer');
    if(fogRow){fogRow.style.display=this.fogActive?'flex':'none';if(vFogT&&this.fogActive)vFogT.textContent=Math.ceil(this.fogTimer/1000)+'s';}
    const sabRow=document.getElementById('sabo-status-row');const vSabT=document.getElementById('vSaboTimer');
    if(sabRow){sabRow.style.display=this.saboActive?'flex':'none';if(vSabT&&this.saboActive)vSabT.textContent=Math.ceil(this.saboTimer/1000)+'s';}
  }

  endGame(win){
    this.gameOver=true;
    // Müziği durdur
    if(this._musicNodes&&this._musicNodes.length){
      this._musicNodes.forEach(n=>{try{n.stop();}catch(e){}});this._musicNodes=[];
    }
    if(this._musicInterval){clearInterval(this._musicInterval);this._musicInterval=null;}
    // Pause menüsünü kapat
    const pm=document.getElementById('pause-menu');if(pm)pm.classList.remove('open');
    const ho=document.getElementById('help-overlay');if(ho)ho.classList.remove('open');
    // Zor'da kazanma başarımı
    if(win&&(this._diffKey==='hard'))this._checkAchievements();
    if(win&&(this._diffKey==='hard')){
      const unlock=(id,icon,title,desc)=>{
        if(this.achievements.has(id))return;this.achievements.add(id);SFX.play('quiz_ok');
        const wrap=document.getElementById('achiev-wrap');if(!wrap)return;
        const el=document.createElement('div');el.className='achiev';
        el.innerHTML=`<div class="achiev-icon">${icon}</div><div><div class="achiev-title">🏅 ${title}</div><div class="achiev-desc">${desc}</div></div>`;
        wrap.appendChild(el);setTimeout(()=>el.remove(),5000);
      };
      unlock('hardwin','💎','Demir İrade','ZOR modda oyunu kazandın!');
    }
    SFX.play(win?'upgrade':'bigboom');
    if(win){this.cameras.main.flash(600,80,255,80,false);}
    else{this.cameras.main.shake(500,0.018);}
    const mins=Math.floor(this.totalTime/60000);
    const secs=Math.floor((this.totalTime%60000)/1000);
    const timeStr=mins+'d '+(secs<10?'0':'')+secs+'s';
    // Skor hesapla
    const score=win?Math.floor(this.pKills*120+this.pAge*800+(AGES[this.pAge].year-1990)*50-this.totalTime/1000*2):Math.floor(this.pKills*60);
    const diffLabel=DIFFICULTY[this._diffKey||'normal'].label;
    // Başarım sayısı
    const achCount=this.achievements.size;
    document.getElementById('etit').textContent=win?'ZAFER!':'YENİLGİ';
    document.getElementById('etit').className='etit '+(win?'win':'lose');
    document.getElementById('esub').textContent=win?'TSK düşmanı mağlup etti!':'Üs düşman eline geçti...';
    document.getElementById('end-rib').textContent=diffLabel+' ZORLUK'+(this.campaignMode?' — KAMPANYA':'');
    const st=document.getElementById('end-stats');
    if(st)st.innerHTML=`
      <div class="estat"><div class="estat-v">${this.pKills}</div><div class="estat-l">TSK İmhası</div></div>
      <div class="estat"><div class="estat-v">${this.eKills}</div><div class="estat-l">Düşman Kaybı</div></div>
      <div class="estat"><div class="estat-v">${AGES[this.pAge].year}</div><div class="estat-l">Son Çağ</div></div>
      <div class="estat"><div class="estat-v">${this.waveCount}</div><div class="estat-l">Atlatılan Dalga</div></div>
      <div class="estat"><div class="estat-v">${timeStr}</div><div class="estat-l">Toplam Süre</div></div>
      <div class="estat"><div class="estat-v">${Math.floor(this.totalDmgDealt)}</div><div class="estat-l">Toplam Hasar</div></div>
      <div class="estat"><div class="estat-v">${this.reinforceCount}</div><div class="estat-l">Takviye Geldi</div></div>
      <div class="estat"><div class="estat-v">${achCount}</div><div class="estat-l">Başarım</div></div>
      <div class="estat"><div class="estat-v">${this.streakCount>0?this.streakCount+'x':'-'}</div><div class="estat-l">Maks Seri</div></div>`;
    const scoreEl=document.getElementById('end-score');
    if(scoreEl)scoreEl.textContent=score>0?score.toLocaleString()+' PUAN':'';
    // Skor kaydet
    if(win)this._saveScore(score);
    const rankEl=document.getElementById('end-rank');
    if(rankEl&&win){const scores=this._loadScores();const rank=scores.findIndex(s=>s.score===score)+1;if(rank>0)rankEl.textContent='Sıralama: #'+rank;}
    // Kampanya devam butonu
    if(this.campaignMode&&win&&this.campaignMission<CAMPAIGN_MISSIONS.length-1){
      const cb=document.getElementById('end-camp-btn');
      if(cb)cb.style.display='inline-block';
    }
    document.getElementById('endscreen').classList.add('show');
  }

  _triggerDefenseLine(side){
    const turrets=(side==='p'?this.pTurrets:this.eTurrets).filter(t=>t.alive);
    const enemies=this.units.filter(u=>u.alive&&u.side!==side);
    if(!turrets.length||!enemies.length)return;

    const now=this.simTime;
    let fired=0;

    turrets.forEach(t=>{
      // En yakın düşmanı bul (menzil dışında olsa bile)
      const sorted=[...enemies].sort((a,b)=>Math.abs(a.x-t.x)-Math.abs(b.x-t.x));
      const target=sorted[0];
      if(!target)return;

      // Hedef yönünü hesapla ve namluyu döndür
      t.aimAngle=Math.atan2((target.y-8)-(t.y-7),target.x-t.x);
      const mx=t.x+Math.cos(t.aimAngle)*16;
      const my=(t.y-7)+Math.sin(t.aimAngle)*16;
      const spd=640;
      const pdx=target.x-mx,pdy=(target.y-8)-my;
      const pd=Math.sqrt(pdx*pdx+pdy*pdy)||1;

      // %150 hasar — savunma hattı atışı güçlü
      this.projs.push({
        x:mx,y:my,vx:(pdx/pd)*spd,vy:(pdy/pd)*spd,
        dmg:Math.ceil(t.dmg*1.5),side:t.side,
        tu:target,tb:null,tt:null,dead:false,t:'heavy',homing:true
      });
      t.lastAtk=this.simTime; // cooldown sıfırla
      fired++;
    });

    if(fired>0){
      // Cooldown ayarla (5 saniye)
      if(side==='p')this.pDefLineCd=5000;
      else this.eDefLineCd=5000;

      this.cameras.main.shake(150,0.008);
      SFX.play('cannon');SFX.play('cannon');
      const msg=side==='p'
        ?'🎯 SAVUNMA HATTI! '+fired+' kule senkron ateşledi!'
        :'☠ Düşman savunma hattı devreye girdi!';
      showToast(msg,side==='p'?'#44aaff':'#ff5533');
    }
  }

  repairBase(){
    if(this.paused||this.gameOver)return;
    if(this.repairCd>0){showToast('Onarım bekleniyor! '+Math.ceil(this.repairCd/1000)+'s','#ffaa44');return;}
    const cost=200+this.pAge*100;
    if(this.pGold<cost){showToast('Yetersiz para! '+cost+'₺ gerekli','#ff4444');return;}
    if(this.pBase.hp>=this.pBase.maxHP){showToast('Üs tam sağlıkta!','#44ff88');return;}
    const heal=Math.ceil(this.pBase.maxHP*.15);
    this.pGold-=cost;
    this.pBase.hp=Math.min(this.pBase.maxHP,this.pBase.hp+heal);
    this.repairCd=this.repairCdMax;
    SFX.play('repair');
    showToast('🔧 Üs onarıldı! +'+heal+' HP','#44aaff');
    this.updateHUD();
  }

  toggleSound(){
    SFX.on=!SFX.on;
    const btn=document.getElementById('btnSnd');
    if(btn)btn.textContent=SFX.on?'🔊 Ses':'🔇 Sessiz';
    showToast(SFX.on?'🔊 Ses açık':'🔇 Ses kapalı','#aaa');
  }

  _sendAIWave(){
    const pool=REG.filter(d=>d.s==='e'&&d.a<=this.eAge);
    if(!pool.length)return;
    const waveSize=Math.min(6,2+this.eAge+Math.floor(this.waveCount/3));
    showToast('⚠ DÜŞMAN DALGASI '+this.waveCount+'! ('+waveSize+' birim)','#ff3300');
    for(let i=0;i<waveSize;i++){
      this.time.delayedCall(i*480,()=>{
        if(this.gameOver||this.paused)return;
        const eCount=this.units.filter(u=>u.alive&&u.side==='e').length;
        if(eCount>=MAX_UNITS_PER_SIDE)return;
        const aff=pool.filter(d=>d.c<=this.aiGold);
        if(!aff.length)return;
        const chosen=aff.sort((a,b)=>b.c-a.c)[0];
        this.aiGold-=chosen.c;
        this.spawnUnit(chosen,'e',ESX,chosen.air?AIR_Y:GND-18);
      });
    }
  }

  // ── HIZ KONTROLÜ ─────────────────────────
  toggleSpeed(){
    if(this.gameSpeed===1)this.gameSpeed=2;
    else if(this.gameSpeed===2)this.gameSpeed=3;
    else this.gameSpeed=1;
    const btn=document.getElementById('btnSpd');
    const labels={1:'⏩ 1× Hız',2:'⏩ 2× Hız',3:'⚡ 3× Hız'};
    if(btn){
      btn.textContent=labels[this.gameSpeed];
      btn.classList.remove('fast');
      if(this.gameSpeed>=2)btn.classList.add('fast');
      if(this.gameSpeed===3)btn.style.background='linear-gradient(135deg,#ff6600,#cc0000)';
      else if(this.gameSpeed===2)btn.style.background='';
      else btn.style.background='';
    }
    showToast(labels[this.gameSpeed],'#8c8');
  }

  // ── HAVA DESTEĞİ ────────────────────────
  airSupport(){
    if(this.paused||this.gameOver)return;
    if(this.airSupCd>0){showToast('Hava desteği hazır değil! '+Math.ceil(this.airSupCd/1000)+'s','#88aaff');return;}
    const cost=600+this.pAge*200;
    if(this.pGold<cost){showToast('Yetersiz para! '+cost+'₺ gerekli','#ff4444');return;}
    this.pGold-=cost;this.airSupCd=this.airSupCdMax;
    showToast('✈ HAVA DESTEĞİ GELİYOR!','#44aaff');
    SFX.play('ult');
    const bombCount=5+this.pAge;
    for(let i=0;i<bombCount;i++){
      const bx=EBX-30-Math.random()*580;
      this.time.delayedCall(i*320+Math.random()*180,()=>{
        if(this.gameOver)return;
        const dmg=180+this.pAge*55;const r=58;
        this.particles.push({x:bx,y:GND-15,r:r,life:580,maxLife:580,type:'exp'});
        this.particles.push({x:bx,y:GND-15,r:r*1.6,life:340,maxLife:340,type:'ring'});
        this.spawnExplosionSparks(bx,GND-15);
        this.cameras.main.shake(90,0.006);SFX.play('boom');
        this.units.filter(u=>u.alive&&u.side==='e'&&Math.abs(u.x-bx)<r).forEach(u=>this.applyDamage(u,dmg));
      });
    }
    this.updateHUD();
    this._checkAchievements();
  }

  // ── MAYIN DÖŞE ──────────────────────────
  placeMine(){
    if(this.paused||this.gameOver)return;
    if(this.mineCd>0){showToast('Mayın hazır değil! '+Math.ceil(this.mineCd/1000)+'s','#ffaa44');return;}
    const cost=150;
    if(this.pGold<cost){showToast('Yetersiz para! '+cost+'₺ gerekli','#ff4444');return;}
    this.pGold-=cost;this.mineCd=this.mineCdMax;
    // Düşman hattının ortasına döşe
    const mx=PBX+200+Math.random()*350;
    this.mines.push({x:mx,alive:true});
    this.spawnFloatText(mx,GND-20,'💣','#ffaa00');
    showToast('💣 Mayın döşendi!','#ffaa44');
    this.updateHUD();
  }

  // ── MÜTTEFİK TAKVİYE ────────────────────
  _sendReinforcements(){
    if(this.gameOver)return;
    const pCount=this.units.filter(u=>u.alive&&u.side==='p').length;
    if(pCount>=MAX_UNITS_PER_SIDE)return;
    const pool=REG.filter(d=>d.s==='p'&&d.a<=this.pAge&&d.c<=400);
    if(!pool.length)return;
    const chosen=pool[Math.floor(Math.random()*pool.length)];
    this.spawnUnit(chosen,'p',PSX,chosen.air?AIR_Y:GND-18);
    showToast('🚁 Müttefik Takviye: '+chosen.nm+' gönderildi!','#44aaff');
  }

  // ── İSTATİSTİK PANELİ ───────────────────
  toggleStats(){
    if(this.gameOver)return;
    const sp=document.getElementById('stats-panel');
    if(!sp)return;
    const opening=!sp.classList.contains('open');
    if(opening){
      this.paused=true;sp.classList.add('open');
      const mins=Math.floor(this.totalTime/60000);
      const secs=Math.floor((this.totalTime%60000)/1000);
      const pAlive=this.units.filter(u=>u.alive&&u.side==='p').length;
      const eAlive=this.units.filter(u=>u.alive&&u.side==='e').length;
      const grid=document.getElementById('stats-grid');
      if(grid)grid.innerHTML=`
        <div class="stat-card"><div class="sv">${this.pKills}</div><div class="sl">TSK İmha</div></div>
        <div class="stat-card"><div class="sv">${this.eKills}</div><div class="sl">Düşman Kaybı</div></div>
        <div class="stat-card"><div class="sv">${Math.floor(this.totalDmgDealt)}</div><div class="sl">Toplam Hasar</div></div>
        <div class="stat-card"><div class="sv">${pAlive}</div><div class="sl">TSK Sahada</div></div>
        <div class="stat-card"><div class="sv">${eAlive}</div><div class="sl">Düşman Sahada</div></div>
        <div class="stat-card"><div class="sv">${this.waveCount}</div><div class="sl">Atlatılan Dalga</div></div>
        <div class="stat-card"><div class="sv">${AGES[this.pAge].year}</div><div class="sl">Mevcut Çağ</div></div>
        <div class="stat-card"><div class="sv">${mins}d ${secs<10?'0':''}${secs}s</div><div class="sl">Geçen Süre</div></div>
        <div class="stat-card"><div class="sv">${this.achievements.size}</div><div class="sl">Başarım</div></div>
        <div class="stat-card"><div class="sv">${Math.round(this.morale)}</div><div class="sl">Moral Seviyesi</div></div>
        <div class="stat-card"><div class="sv">${this.units.filter(u=>u.alive&&u.side==='p'&&u.veteran).length}</div><div class="sl">Veteran Birlik</div></div>
        <div class="stat-card"><div class="sv">${this.targetPriority==='nearest'?'EN YAKIN':this.targetPriority==='weakest'?'EN ZAYIF':this.targetPriority==='strongest'?'EN GÜÇLÜ':'EN İLERİ'}</div><div class="sl">Hedef Önceliği</div></div>
        <div class="stat-card"><div class="sv">${this.curWeather==='clear'?'☀ Açık':this.curWeather==='rain'?'🌧 Yağmur':'⛅ Bulutlu'}</div><div class="sl">Hava Durumu</div></div>`;
    }else{
      this.paused=false;sp.classList.remove('open');
    }
  }

  // ── CİNEMATİC AÇILIŞ ────────────────────
  _showCinematic(){
    const cin=document.getElementById('cinematic');
    if(!cin)return;
    document.getElementById('cin-year').textContent=AGES[0].year;
    document.getElementById('cin-name').textContent=AGES[0].name;
    this.paused=true;
    this.time.delayedCall(2200,()=>{
      cin.style.opacity='0';
      this.time.delayedCall(1300,()=>{
        cin.style.display='none';this.paused=false;
      });
    });
  }

  // ── BAŞARIMLAR ───────────────────────────
  _checkAchievements(){
    const unlock=(id,icon,title,desc)=>{
      if(this.achievements.has(id))return;
      this.achievements.add(id);
      SFX.play('quiz_ok');
      const wrap=document.getElementById('achiev-wrap');
      if(!wrap)return;
      const el=document.createElement('div');
      el.className='achiev';
      el.innerHTML=`<div class="achiev-icon">${icon}</div><div><div class="achiev-title">🏅 ${title}</div><div class="achiev-desc">${desc}</div></div>`;
      wrap.appendChild(el);setTimeout(()=>el.remove(),4200);
    };
    if(this.pKills>=10)unlock('k10','💥','İlk 10 İmha','10 düşman birimi imha edildi');
    if(this.pKills>=50)unlock('k50','⚔','Savaş Gazisi','50 düşman birimi imha edildi');
    if(this.pKills>=100)unlock('k100','🔱','Efsane Komutan','100 düşman birimi imha edildi');
    if(this.pAge>=1)unlock('age1','🏛','Modern Çağ','İlk çağ atlaması yapıldı');
    if(this.pAge>=3)unlock('age3','🚀','Dijital Savaşçı','3. çağa ulaşıldı');
    if(this.pAge>=4)unlock('age4','⚡','Gelecek Komutanı','Son çağa ulaşıldı');
    if(this.streakCount>=5)unlock('streak5','🔥','Beşli Seri','5 üst üste kill');
    if(this.streakCount>=10)unlock('streak10','💀','Ölüm Makinesi','10 üst üste kill');
    if(this.waveCount>=5)unlock('wave5','🌊','Dalga Savar','5 düşman dalgasını durdur');
    if(this.pBase.hp>=this.pBase.maxHP*0.9&&this.pKills>=20)unlock('hp90','🏰','Kale Gibi','Üs %90+ HP ile 20 imha');
    if(this.pTurrets.filter(t=>t.alive).length===3)unlock('tur3','🗼','Savunma Hattı','3 kule aktif');
    if(this.airSupCd<this.airSupCdMax)unlock('air1','✈','Hava Kuvvetleri','İlk hava desteği kullanıldı');
    if(this.mines.length===0&&this.reinforceCount>=2)unlock('reinf','🚁','Komuta Merkezi','2 takviye geldi');
    if(this.nukeUsed)unlock('nuke1','☢','Nükleer Güç','Nükleer saldırı kullanıldı');
    if(this.saboActive||this.saboCd>0)unlock('sabo1','💸','Gölge Savaş','Ekonomik sabotaj kullanıldı');
    if(this.pKills>=200)unlock('k200','🏆','Harp Tanrısı','200 düşman birimi imha edildi');
    if(this.waveCount>=10)unlock('wave10','🌊🌊','Tsunami Durdurucu','10 dalga durduruldu');
    if(this.zoneOwner==='p')unlock('zone1','🚩','Toprak Hâkimi','Orta bölge ele geçirildi');
    if(this.droneCd>0)unlock('drone1','🚁','Drone Komutanı','İlk drone sürüsü ateşlendi');
    if(this._lastKillCelebrate>=100)unlock('k100c','🎯','Yüzüncü İmha','100. kill kutlandı');
  }

  // ── KAYIT SİSTEMİ ───────────────────────
  _saveScore(score){
    try{
      const scores=this._loadScores();
      const mins=Math.floor(this.totalTime/60000),secs=Math.floor((this.totalTime%60000)/1000);
      scores.push({score,kills:this.pKills,age:AGES[this.pAge].year,diff:this._diffKey||'normal',time:mins+'d '+(secs<10?'0':'')+secs+'s',date:new Date().toLocaleDateString('tr-TR')});
      scores.sort((a,b)=>b.score-a.score);
      localStorage.setItem('cgbs_scores',JSON.stringify(scores.slice(0,10)));
    }catch(e){}
  }
  _loadScores(){
    try{return JSON.parse(localStorage.getItem('cgbs_scores'))||[];}catch(e){return [];}
  }
  _renderHiScores(){
    const el=document.getElementById('hi-scores-list');
    if(!el)return;
    const scores=this._loadScores();
    if(!scores.length){el.innerHTML='<div style="color:#555">Henüz kayıt yok</div>';return;}
    el.innerHTML='<div style="color:#888;font-size:10px;margin-bottom:6px;letter-spacing:2px">EN İYİ SKORLAR</div>'+
      scores.slice(0,5).map((s,i)=>`<div>${['🥇','🥈','🥉','4.','5.'][i]} <span style="color:#f39c12;font-weight:700">${s.score.toLocaleString()}</span> — ${DIFFICULTY[s.diff].label} — ${s.kills} imha — ${s.date}</div>`).join('');
  }

  // ── EMP DARBESİ ─────────────────────────
  empBlast(){
    if(this.paused||this.gameOver)return;
    if(this.empCd>0){showToast('EMP hazır değil! '+Math.ceil(this.empCd/1000)+'s','#44aaff');return;}
    if(this.pGold<500){showToast('Yetersiz para! 500₺ gerekli','#ff4444');return;}
    this.pGold-=500;this.empCd=this.empCdMax;
    this.empActive=true;this.empTimer=8000;
    // Görsel dalga
    this.particles.push({x:W/2,y:GND-20,r:400,life:500,maxLife:500,type:'ring'});
    this.particles.push({x:W/2,y:GND-20,r:300,life:400,maxLife:400,type:'ring'});
    this.cameras.main.flash(350,0,170,255,false);
    this.cameras.main.shake(200,0.009);
    SFX.play('ult');
    showToast('⚡ EMP! Düşman %65 yavaşladı — 8s','#44aaff');
    this._addLog('⚡ EMP Darbesi ateşlendi');
    this._checkAchievements();
    this.updateHUD();
  }

  // ── AĞIR TOPÇU ──────────────────────────
  heavyArty(){
    if(this.paused||this.gameOver)return;
    if(this.artyCd>0){showToast('Ağır Topçu hazır değil! '+Math.ceil(this.artyCd/1000)+'s','#ffaa44');return;}
    const cost=1200+this.pAge*300;
    if(this.pGold<cost){showToast('Yetersiz para! '+cost+'₺ gerekli','#ff4444');return;}
    this.pGold-=cost;this.artyCd=this.artyCdMax;
    showToast('💥 AĞIR TOPÇU ATEŞ!','#ffaa00');
    SFX.play('bigboom');this.cameras.main.shake(400,0.018);
    // Düşman hattında 4 büyük patlama
    const hits=4+this.pAge;
    for(let i=0;i<hits;i++){
      const bx=EBX-60-Math.random()*500;
      this.time.delayedCall(i*550,()=>{
        if(this.gameOver)return;
        const dmg=300+this.pAge*80;const r=80;
        this.particles.push({x:bx,y:GND-15,r:r,life:700,maxLife:700,type:'exp'});
        this.particles.push({x:bx,y:GND-15,r:r*2,life:450,maxLife:450,type:'ring'});
        for(let j=0;j<6;j++)this.particles.push({x:bx+Phaser.Math.Between(-40,40),y:GND-Phaser.Math.Between(10,60),
          vx:Phaser.Math.Between(-60,60)/100,vy:Phaser.Math.Between(-120,-30)/100,life:600,maxLife:600,type:'smoke'});
        this.spawnExplosionSparks(bx,GND-20);this.cameras.main.shake(180,0.012);SFX.play('bigboom');
        this.units.filter(u=>u.alive&&u.side==='e'&&Math.abs(u.x-bx)<r).forEach(u=>this.applyDamage(u,dmg));
      });
    }
    this._addLog('💥 Ağır Topçu: '+hits+' vuruş');
    this.updateHUD();
  }

  // ── GECE MODU ───────────────────────────
  toggleNight(){
    this.nightMode=!this.nightMode;
    const no=document.getElementById('night-overlay');
    const btn=document.getElementById('btnNight');
    if(no)this.nightMode?no.classList.add('active'):no.classList.remove('active');
    if(btn){btn.textContent=this.nightMode?'☀ Gündüz':'🌙 Gece Modu';
      this.nightMode?btn.classList.remove('day'):btn.classList.add('day');}
    showToast(this.nightMode?'🌙 Gece modu aktif':'☀ Gündüz modu','#8888ff');
  }

  // ── MİNİ HARİTA ─────────────────────────
  _updateMinimap(){
    const canvas=document.getElementById('minimap-canvas');
    if(!canvas)return;
    const ctx=canvas.getContext('2d');
    const W2=120,H2=50,scaleX=W2/W,scaleY=H2/H;
    ctx.clearRect(0,0,W2,H2);
    ctx.fillStyle='rgba(0,0,0,.6)';ctx.fillRect(0,0,W2,H2);
    // Üsler
    ctx.fillStyle='#27ae60';ctx.fillRect(PBX*scaleX-4,H2-8,8,6);
    ctx.fillStyle='#e74c3c';ctx.fillRect(EBX*scaleX-4,H2-8,8,6);
    // Turretler
    [...this.pTurrets,...this.eTurrets].filter(t=>t.alive).forEach(t=>{
      ctx.fillStyle=t.side==='p'?'#2ecc71':'#ff4444';
      ctx.fillRect(t.x*scaleX-2,t.y*scaleY-2,4,4);
    });
    // Birimler
    this.units.filter(u=>u.alive).forEach(u=>{
      ctx.fillStyle=u.side==='p'?(u.def.special?'#f39c12':'#4ade80'):(u.def.t==='veh'?'#ff6b6b':'#ff9999');
      const r=u.def.t==='veh'?2.5:1.5;
      ctx.beginPath();ctx.arc(u.x*scaleX,u.y*scaleY,r,0,Math.PI*2);ctx.fill();
    });
    // Cephe çizgisi
    ctx.strokeStyle='rgba(255,255,255,.3)';ctx.lineWidth=1;
    ctx.beginPath();ctx.moveTo(W2/2,0);ctx.lineTo(W2/2,H2);ctx.stroke();
  }

  // ── SAVAŞ GÜNLÜĞÜ ───────────────────────
  _addLog(msg){
    const log=document.getElementById('combat-log');if(!log)return;
    const el=document.createElement('div');el.className='log-entry';el.textContent=msg;
    log.prepend(el);
    setTimeout(()=>el.remove(),8200);
    // Max 6 satır
    while(log.children.length>6)log.removeChild(log.lastChild);
  }

  // ── BOSS BİRİMİ ─────────────────────────
  _spawnBoss(){
    if(this.gameOver)return;
    const eCount=this.units.filter(u=>u.alive&&u.side==='e').length;
    if(eCount>=MAX_UNITS_PER_SIDE)return;
    // Mevcut çağdaki en pahalı düşman birimini al, HP'yi 3× artır
    const pool=REG.filter(d=>d.s==='e'&&d.a===this.eAge).sort((a,b)=>b.c-a.c);
    if(!pool.length)return;
    const base=pool[0];
    const bossDef={...base,id:base.id+'_boss',nm:'☠ BOSS: '+base.nm,h:base.h*3,d:Math.ceil(base.d*1.5),c:0};
    // Geçici texture olarak aynı texture'ı kullan
    const u=this.spawnUnit(bossDef,'e',ESX,base.air?AIR_Y:GND-18);
    if(u){
      u.hp=bossDef.h;u.maxHP=bossDef.h;u.isBoss=true;
      showToast('☠ BOSS: '+base.nm+' geliyor! (3× HP, 1.5× Hasar)','#ff2200');
      this._addLog('☠ BOSS birimi sahaya çıktı!');
      this.cameras.main.shake(300,0.015);SFX.play('bigboom');
      this._checkAchievements();
    }
  }

  // ── SAVAŞ MÜZİĞİ ────────────────────────
  toggleMusic(){
    if(!SFX.ctx)return;
    if(this._musicNodes.length){
      this._musicNodes.forEach(n=>{try{n.stop();}catch(e){}});
      this._musicNodes=[];
      showToast('🔇 Müzik kapatıldı','#888');return;
    }
    const ctx=SFX.ctx;
    const notes=[110,138,165,196,220];
    let beatIdx=0;
    const beatInterval=setInterval(()=>{
      if(!SFX.on||this.gameOver){clearInterval(beatInterval);return;}
      const osc=ctx.createOscillator();const gain=ctx.createGain();
      osc.connect(gain);gain.connect(ctx.destination);
      osc.frequency.value=notes[beatIdx%notes.length];osc.type='triangle';
      gain.gain.setValueAtTime(0.04,ctx.currentTime);
      gain.gain.exponentialRampToValueAtTime(0.001,ctx.currentTime+0.5);
      osc.start(ctx.currentTime);osc.stop(ctx.currentTime+0.5);
      this._musicNodes.push(osc);beatIdx++;
      // Clean up old nodes
      this._musicNodes=this._musicNodes.filter(n=>{try{n.playbackState!==undefined;return true;}catch(e){return false;}});
    },600);
    this._musicInterval=beatInterval;
    this._musicNodes.push({stop:()=>clearInterval(beatInterval)});
    showToast('🎵 Savaş müziği açık','#44ff88');
  }

  // ── DRONE SÜRÜSÜ ─────────────────────────
  droneSwarm(){
    if(this.paused||this.gameOver)return;
    if(this.droneCd>0){showToast('Drone hazır değil! '+Math.ceil(this.droneCd/1000)+'s','#44aaff');return;}
    if(this.pGold<700){showToast('Yetersiz altın! 700₺ gerekli','#ff4444');return;}
    this.pGold-=700;this.droneCd=this.droneCdMax;
    showToast('🚁 Drone Sürüsü fırlatıldı!','#44aaff');
    this._addLog('🚁 6 Drone ateşlendi');
    SFX.play('ult');this.cameras.main.shake(150,0.006);
    const dmg=180+this.pAge*55;
    for(let i=0;i<6;i++){
      this.time.delayedCall(i*300+Math.random()*100,()=>{
        if(this.gameOver)return;
        const tgt=this.units.filter(u=>u.alive&&u.side==='e');
        if(!tgt.length)return;
        const t=tgt[Math.floor(Math.random()*tgt.length)];
        this.particles.push({x:t.x+Phaser.Math.Between(-40,40),y:t.y-40,r:35,life:280,maxLife:280,type:'exp'});
        this.applyDamage(t,dmg);
        this.cameras.main.shake(60,0.004);
        SFX.play('bullet');
      });
    }
    this.updateHUD();
  }

  // ── SİS BOMBASI ──────────────────────────
  fogBomb(){
    if(this.paused||this.gameOver)return;
    if(this.fogCd>0){showToast('Sis hazır değil! '+Math.ceil(this.fogCd/1000)+'s','#aaa');return;}
    if(this.pGold<400){showToast('Yetersiz altın! 400₺ gerekli','#ff4444');return;}
    this.pGold-=400;this.fogCd=this.fogCdMax;
    this.fogActive=true;this.fogTimer=12000;
    const fo=document.getElementById('fog-overlay');if(fo)fo.classList.add('active');
    // Sis partikülleri
    for(let i=0;i<12;i++)this.particles.push({
      x:EBX-Math.random()*600,y:GND-Math.random()*80,
      vx:(Math.random()-.5)*.03,vy:-Math.random()*.02,
      life:4000+Math.random()*3000,maxLife:7000,type:'smoke'
    });
    SFX.play('spawn');this.cameras.main.flash(200,180,180,200,false);
    showToast('🌫 Sis Bombası! Düşman görüşü engellendi (12s)','#aaaccc');
    this._addLog('🌫 Sis Bombası atıldı');
    this.updateHUD();
  }

  // ── NÜKLEER SEÇENEK ──────────────────────
  nuclearStrike(){
    if(this.paused||this.gameOver)return;
    if(this.nukeUsed){showToast('Nükleer seçenek yalnızca 1 kez kullanılabilir!','#ff4444');return;}
    if(this.pGold<5000){showToast('Yetersiz altın! 5000₺ gerekli','#ff4444');return;}
    this.pGold-=5000;this.nukeUsed=true;
    showToast('☢ NÜKLEER SALDIRI BAŞLATILDI!','#ff0000');
    this._addLog('☢ Nükleer bomba ateşlendi!');
    SFX.play('bigboom');
    this.cameras.main.shake(600,0.03);this.cameras.main.flash(800,255,200,50,false);
    // Tüm düşman birimleri yüksek hasar al
    this.time.delayedCall(400,()=>{
      if(this.gameOver)return;
      const dmg=2500+this.pAge*500;
      this.units.filter(u=>u.alive&&u.side==='e').forEach(u=>{
        this.particles.push({x:u.x,y:u.y,r:60,life:600,maxLife:600,type:'exp'});
        this.applyDamage(u,dmg);
      });
      // Dev nükleer patlama efekti
      for(let i=0;i<5;i++)this.particles.push({x:EBX-Math.random()*600,y:GND-20,r:150+i*40,life:900,maxLife:900,type:'ring'});
      this.cameras.main.shake(500,0.025);SFX.play('bigboom');
      // Düşman üssüne de hasar
      this.applyBaseDamage(this.eBase,800+this.pAge*150);
    });
    this.updateHUD();
  }

  // ── LOJİSTİK DESTEK ──────────────────────
  logisticSupport(){
    if(this.paused||this.gameOver)return;
    if(this.logiCd>0){showToast('Lojistik hazır değil! '+Math.ceil(this.logiCd/1000)+'s','#44ee88');return;}
    if(this.pGold<500){showToast('Yetersiz altın! 500₺ gerekli','#ff4444');return;}
    this.pGold-=500;this.logiCd=this.logiCdMax;
    let healed=0;
    this.units.filter(u=>u.alive&&u.side==='p').forEach(u=>{
      const h=Math.ceil(u.maxHP*.25);u.hp=Math.min(u.maxHP,u.hp+h);
      this.spawnFloatText(u.x,u.y-25,'+'+h,'#44ee88');healed+=h;
    });
    // Üs de biraz iyileşir
    const baseHeal=Math.ceil(this.pBase.maxHP*.05);
    this.pBase.hp=Math.min(this.pBase.maxHP,this.pBase.hp+baseHeal);
    SFX.play('repair');this.cameras.main.flash(300,0,200,80,false);
    showToast('🏥 Lojistik Destek! +'+healed+' toplam iyileşme','#44ee88');
    this._addLog('🏥 Lojistik: +'+healed+' HP iyileşme');
    this.updateHUD();
  }

  // ── EKONOMİK SABOTAJ ─────────────────────
  economicSabotage(){
    if(this.paused||this.gameOver)return;
    if(this.saboCd>0){showToast('Sabotaj hazır değil! '+Math.ceil(this.saboCd/1000)+'s','#ffaa44');return;}
    if(this.pGold<800){showToast('Yetersiz altın! 800₺ gerekli','#ff4444');return;}
    this.pGold-=800;this.saboCd=this.saboCdMax;
    this.saboActive=true;this.saboTimer=15000;
    // Düşmanın mevcut altınını yarıya indir
    this.aiGold=Math.floor(this.aiGold*.5);
    SFX.play('ult');
    showToast('💸 Sabotaj! Düşman ekonomisi 15s bloke!','#ffaa00');
    this._addLog('💸 Ekonomik Sabotaj: Düşman geliri kesildi');
    this.updateHUD();
  }

  // ── DURAKLAT / DEVAM ─────────────────────
  togglePause(){
    if(this.gameOver)return;
    this.paused=!this.paused;
    const pm=document.getElementById('pause-menu');
    if(pm)this.paused?pm.classList.add('open'):pm.classList.remove('open');
  }
  resumeGame(){this.paused=false;const pm=document.getElementById('pause-menu');if(pm)pm.classList.remove('open');}
  restartGame(){
    if(!confirm('Oyunu yeniden başlatmak istiyor musunuz?'))return;
    location.reload();
  }

  // ── YARDIM PANELİ ────────────────────────
  toggleHelp(){
    const hp=document.getElementById('help-overlay');if(!hp)return;
    hp.classList.toggle('open');
    this.paused=hp.classList.contains('open');
  }

  // ── BÖLGE KONTROLÜ ───────────────────────
  _updateZoneControl(dt){
    const zoneX=W/2;const zoneR=100;
    const pInZone=this.units.filter(u=>u.alive&&u.side==='p'&&Math.abs(u.x-zoneX)<zoneR).length;
    const eInZone=this.units.filter(u=>u.alive&&u.side==='e'&&Math.abs(u.x-zoneX)<zoneR).length;
    const fl=document.getElementById('zone-flag');const zf=document.getElementById('zone-fill');
    let newOwner=null;
    if(pInZone>eInZone)newOwner='p';
    else if(eInZone>pInZone)newOwner='e';
    if(newOwner&&newOwner!==this.zoneOwner){
      this.zoneTimer+=dt;
      if(this.zoneTimer>=this.zoneCaptureTime){
        this.zoneOwner=newOwner;this.zoneTimer=0;
        showToast(newOwner==='p'?'🚩 Bölge ele geçirildi! +25₺/gelir':'🔴 Düşman bölgeyi aldı!',newOwner==='p'?'#44ee88':'#ff4444');
        if(fl)fl.textContent=newOwner==='p'?'🟢':'🔴';
      }
    }else{this.zoneTimer=Math.max(0,this.zoneTimer-dt*.5);}
    if(zf){const pct=newOwner==='p'?Math.min(100,this.zoneTimer/this.zoneCaptureTime*100):newOwner==='e'?Math.max(0,100-this.zoneTimer/this.zoneCaptureTime*100):50;
      zf.style.width=pct+'%';zf.style.background=this.zoneOwner==='p'?'linear-gradient(90deg,#27ae60,#2ecc71)':this.zoneOwner==='e'?'linear-gradient(90deg,#c0392b,#e74c3c)':'linear-gradient(90deg,#888,#aaa)';}
    // Bölge sahibine bonus gelir
    if(this.zoneOwner){
      this.zoneGoldTimer+=dt;
      if(this.zoneGoldTimer>=3000){
        this.zoneGoldTimer=0;
        if(this.zoneOwner==='p'){this.pGold+=25;this.spawnFloatText(zoneX,GND-60,'+25₺ Bölge','#44ee88');}
        else{this.aiGold+=25;}
      }
    }
  }

  // ── BİRİM GÖLGELERİ ──────────────────────
  _drawUnitShadows(){
    const g=this.hpGfx;
    this.units.filter(u=>u.alive&&!u.def.air).forEach(u=>{
      g.fillStyle(0x000000,.2);
      const sw=u.def.t==='veh'?38:20;
      g.fillEllipse(u.x,GND+3,sw,6);
    });
  }

  // ── HP BARLARI ───────────────────────────
  _drawUnitHPBars(){
    const g=this.hpGfx;
    this.units.filter(u=>u.alive).forEach(u=>{
      const pct=u.hp/u.maxHP;
      if(pct>=1)return;
      const bw=30,bh=4,bx=u.x-bw/2,by=u.y-(u.def.air?36:30);
      g.fillStyle(0x000000,.65);g.fillRect(bx-1,by-1,bw+2,bh+2);
      const col=pct>.6?0x44ee88:pct>.3?0xffaa00:0xff3333;
      g.fillStyle(col,.9);g.fillRect(bx,by,Math.ceil(bw*pct),bh);
      if(u.def.special){g.lineStyle(1,0xf39c12,.7);g.strokeRect(bx-1,by-1,bw+2,bh+2);}
    });
  }

  // ── ATEŞ / DUMAN EFEKTİ HASARLI ARAÇLAR ──
  _drawFireEffects(dt){
    this.units.filter(u=>u.alive&&u.def.t==='veh'&&u.hp/u.maxHP<.3).forEach(u=>{
      if(Math.random()<.08){
        this.particles.push({x:u.x+Phaser.Math.Between(-12,12),y:u.y-20,vx:(Math.random()-.5)*.04,vy:-Math.random()*.08,life:600+Math.random()*400,maxLife:1000,type:'smoke'});
      }
    });
  }

  // ── YILDIZ PARTİKÜLLERİ ─────────────────
  _drawStars(dt){
    const g=this.fxGfx;
    this._stars.forEach(s=>{
      s.t+=dt*.001;
      const a=.3+Math.sin(s.t)*0.25;
      g.fillStyle(0xffffff,a);g.fillCircle(s.x,s.y,s.r);
    });
  }

  // ── HOVER TOOLTIP ────────────────────────
  _onPointerMove(wx,wy){
    const tt=document.getElementById('unit-tooltip');if(!tt)return;
    const u=this.units.find(u=>u.alive&&Math.abs(u.x-wx)<24&&Math.abs(u.y-wy)<24);
    if(!u){tt.style.display='none';return;}
    tt.style.display='block';tt.style.left=(wx+12)+'px';tt.style.top=(wy-60)+'px';
    const kc=u.killCount||0;
    const rankIcon=u.veteran?'🎖 VETERAN ':kc>=3?'⭐ UZMAN ':'';
    const extras=(u._rainSlow>0?' 🌧':(u._stunned>0?' ⚡STUN':''))+(u.veteran?' +%20Atk':'');
    tt.innerHTML=`<b>${rankIcon}${u.def.nm}</b><br>HP: ${Math.ceil(u.hp)}/${u.maxHP}<br>Hasar: ${u.def.d} | Menzil: ${u.def.r}<br>Öldürme: ${kc}${extras}`;
  }

  // ── BİRİM SAT (sağ tık) ──────────────────
  _sellUnit(wx,wy){
    if(this.paused||this.gameOver)return;
    const u=this.units.find(u=>u.alive&&u.side==='p'&&Math.abs(u.x-wx)<28&&Math.abs(u.y-wy)<28);
    if(!u)return;
    const refund=Math.ceil(u.def.c*.5);
    this.pGold+=refund;u.alive=false;
    if(u.spr)u.spr.destroy();
    this.spawnFloatText(u.x,u.y-20,'+'+refund+'₺ (Sat)','#f5a623');
    showToast('💱 '+u.def.nm+' satıldı: +'+refund+'₺','#f5a623');
    this._addLog('💱 '+u.def.nm+' satıldı');
    this.updateHUD();
  }

  // ── KİLL KUTLAMA ─────────────────────────
  _checkKillCelebrate(){
    const milestones=[50,100,200,350,500,750,1000];
    for(const ms of milestones){
      if(this.pKills>=ms&&this._lastKillCelebrate<ms){
        this._lastKillCelebrate=ms;
        const kc=document.getElementById('kill-celebrate');
        if(kc){
          kc.textContent='🏆 '+ms+' İMHA!';kc.style.opacity='1';
          setTimeout(()=>{kc.style.opacity='0';},2500);
        }
        this.cameras.main.flash(400,255,200,50,false);
        SFX.play('upgrade');
        // Kill başarımı ile skor çarpanı artır
        this.scoreMultiplier=Math.min(5,this.scoreMultiplier+.5);
        this.scoreMultTimer=15000;
        const sm=document.getElementById('score-mult');
        if(sm){sm.textContent='⚡ ×'+this.scoreMultiplier+' Skor!';sm.classList.add('show');}
        break;
      }
    }
  }

  // ── KOMBİNASYON KİLL SİSTEMİ ─────────────
  _registerComboKill(){
    const now=this.simTime;
    if(!this._comboKills)this._comboKills=[];
    if(!this._lastComboMsg)this._lastComboMsg=0;
    this._comboKills.push(now);
    // Son 4 saniyedeki öldürmeler
    this._comboKills=this._comboKills.filter(t=>now-t<4000);
    const cnt=this._comboKills.length;
    if(cnt>=3&&now-this._lastComboMsg>3000){
      this._lastComboMsg=now;
      const msgs={3:'TRIPLE KİLL! ×1.5₺',5:'PENTA KİLL! ×2₺',8:'RAMPAGE! ×3₺',12:'UNSTOPPABLE! ×5₺'};
      const bonus={3:1.5,5:2,8:3,12:5};
      let key=3;for(const k of[12,8,5,3])if(cnt>=k){key=k;break;}
      const baseRw=this.units.filter(u=>!u.alive&&u.side==='e').slice(-1)[0]?.def.rw||80;
      const extraGold=Math.floor(baseRw*bonus[key]);
      this.pGold+=extraGold;
      const banner=document.getElementById('kill-celebrate');
      if(banner){banner.textContent='💥 '+msgs[key]+' +'+extraGold+'₺';banner.style.opacity='1';setTimeout(()=>banner.style.opacity='0',2200);}
      this.cameras.main.shake(200,0.008,false);
      SFX.play('upgrade');
    }
  }

  // ── HEDEF ÖNCELİK DÖNGÜSÜ ───────────────
  cycleTargetPriority(){
    const modes=['nearest','weakest','strongest','closest_base'];
    const labels={nearest:'EN YAKIN',weakest:'EN ZAYIF',strongest:'EN GÜÇLÜ',closest_base:'EN İLERİ'};
    const idx=modes.indexOf(this.targetPriority);
    this.targetPriority=modes[(idx+1)%modes.length];
    const el=document.getElementById('targetTxt');
    if(el)el.textContent=labels[this.targetPriority];
    showToast('🎯 Hedef Önceliği: '+labels[this.targetPriority],'#dd88ff');
  }

  // ── ARAŞTIRMA AĞACI ──────────────────────
  _getResearchDefs(){
    return{
      war:[
        {id:'w1',name:'Keskin Nişan',icon:'🎯',desc:'Tüm birim hasarı +%15',cost:400,effect:()=>{this.units.filter(u=>u.alive&&u.side==='p').forEach(u=>u.def={...u.def,d:Math.ceil(u.def.d*1.15)});}},
        {id:'w2',name:'Hızlı Ateş',icon:'💥',desc:'Tüm birimlerin ateş hızı +%20',cost:600,req:'w1',effect:()=>{this.units.filter(u=>u.alive&&u.side==='p').forEach(u=>u.def={...u.def,rt:Math.floor(u.def.rt*.83)});}},
        {id:'w3',name:'Uzun Menzil',icon:'🔭',desc:'Tüm menzil +%25',cost:800,req:'w2',effect:()=>{this.units.filter(u=>u.alive&&u.side==='p').forEach(u=>u.def={...u.def,r:Math.ceil(u.def.r*1.25)});}},
      ],
      def:[
        {id:'d1',name:'Zırh Kaplama',icon:'🛡',desc:'Tüm dost birim HP +%20',cost:400,effect:()=>{this.units.filter(u=>u.alive&&u.side==='p').forEach(u=>{u.maxHP=Math.ceil(u.maxHP*1.2);u.hp=Math.min(u.maxHP,Math.ceil(u.hp*1.2));});}},
        {id:'d2',name:'Üs Tahkimatı',icon:'🏰',desc:'Üs max HP +%30 ve onarım',cost:600,req:'d1',effect:()=>{this.pBase.maxHP=Math.ceil(this.pBase.maxHP*1.3);this.pBase.hp=Math.min(this.pBase.maxHP,this.pBase.hp+Math.ceil(this.pBase.maxHP*.15));}},
        {id:'d3',name:'Reaktif Zırh',icon:'⚙',desc:'Hasar alınca %25 geri yansıtılır',cost:800,req:'d2',effect:()=>{this._reactiveArmor=true;}},
      ],
      eco:[
        {id:'e1',name:'Savaş Ekonomisi',icon:'💰',desc:'Pasif gelir +%25',cost:350,effect:()=>{AGES.forEach(a=>a.inc=Math.ceil(a.inc*1.25));}},
        {id:'e2',name:'Ganimetçi',icon:'💎',desc:'Öldürme başı ödül +%30',cost:550,req:'e1',effect:()=>{this._lootBonus=1.3;}},
        {id:'e3',name:'Savaş Tahvili',icon:'📈',desc:'Faiz oranı 2× olur',cost:750,req:'e2',effect:()=>{this._interestMult=2;}},
      ],
    };
  }
  toggleResearch(){
    const p=document.getElementById('research-panel');if(!p)return;
    const open=p.classList.toggle('open');
    if(open)this._renderResearch();
  }
  setResTab(tab){
    this._resTab=tab;
    ['war','def','eco'].forEach(t=>{const el=document.getElementById('rtab-'+t);if(el)el.classList.toggle('active',t===tab);});
    this._renderResearch();
  }
  _renderResearch(){
    const grid=document.getElementById('res-grid');if(!grid)return;
    const defs=this._getResearchDefs()[this._resTab]||[];
    const owned=this.research[this._resTab]||{};
    grid.innerHTML=defs.map(r=>{
      const isOwned=!!owned[r.id];
      const isLocked=r.req&&!owned[r.req];
      const canAff=this.pGold>=r.cost;
      const cls=isOwned?'owned':isLocked?'locked':'';
      const costTxt=isOwned?'✓ ALINDI':isLocked?('🔒 '+r.name+' gerekli'):(canAff?r.cost+'₺':'💸 '+r.cost+'₺');
      return`<div class="res-item ${cls}" onclick="!${isOwned}&&!${isLocked}&&window._buyResearch&&window._buyResearch('${this._resTab}','${r.id}')">
        <div class="res-icon">${r.icon}</div>
        <div class="res-info"><div class="res-name">${r.name}</div><div class="res-desc">${r.desc}</div></div>
        <div class="res-cost" style="color:${isOwned?'#2ecc71':canAff&&!isLocked?'#f5a623':'#e74c3c'}">${costTxt}</div>
      </div>`;
    }).join('');
  }
  buyResearch(cat,id){
    if(this.paused||this.gameOver)return;
    const defs=this._getResearchDefs()[cat]||[];
    const r=defs.find(x=>x.id===id);if(!r)return;
    if(this.research[cat][id])return;
    if(r.req&&!this.research[cat][r.req]){showToast('🔒 Önce önceki araştırmayı al!','#ff8844');return;}
    if(this.pGold<r.cost){showToast('💸 Yetersiz altın! '+r.cost+'₺ gerekli','#ff4444');return;}
    this.pGold-=r.cost;
    this.research[cat][id]=true;
    r.effect();
    showToast('🔬 '+r.name+' araştırıldı!','#88ddff');
    this._addLog('🔬 Araştırma: '+r.name);
    SFX.play('upgrade');
    this.cameras.main.flash(300,80,180,255,false);
    this._renderResearch();
    this.updateHUD();
  }

  // ── DEMİR PERDE ──────────────────────────
  activateIronCurtain(){
    if(this.paused||this.gameOver)return;
    if(this.ironCurtainCd>0){showToast('⏳ Demir Perde bekleniyor: '+Math.ceil(this.ironCurtainCd/1000)+'s','#8888ff');return;}
    this.ironCurtainActive=true;this.ironCurtainTimer=8000;
    this.ironCurtainCd=this.ironCurtainCdMax;
    const icf=document.getElementById('iron-curtain-flash');if(icf)icf.classList.add('active');
    showToast('🛡 DEMİR PERDE AKTİF! 8s boyunca %70 hasar azaltma!','#4488ff');
    this._addLog('🛡 Demir Perde aktifleştirildi');
    SFX.play('ult');
    this.cameras.main.flash(400,0,80,200,false);
    this.cameras.main.shake(200,0.006,false);
    // Tüm dost birimlere mavi parıltı
    this.units.filter(u=>u.alive&&u.side==='p').forEach(u=>{
      if(u.spr){u.spr.setTint(0x4488ff);this.time.delayedCall(8000,()=>{if(u.alive&&u.spr)u.spr.clearTint();});}
    });
  }

  // ── TIKLAMALı TOPÇU NİŞAN ────────────────
  toggleArtilleryMode(){
    if(this.paused||this.gameOver)return;
    this.artilleryMode=!this.artilleryMode;
    const btn=document.getElementById('btnArtilleryMode');
    if(btn){
      btn.style.background=this.artilleryMode?'linear-gradient(135deg,#6a1a00,#aa3300)':'linear-gradient(135deg,#3a1a0a,#6a3010)';
      btn.style.borderColor=this.artilleryMode?'rgba(255,80,30,.8)':'rgba(255,130,60,.4)';
    }
    if(this.artilleryMode){
      showToast('🎯 Topçu Nişan Modu: Düşman hattına TIKLAm!','#ffaa66');
      document.getElementById('root').style.cursor='crosshair';
    }else{
      document.getElementById('root').style.cursor='';
    }
  }
  _fireClickArtillery(wx,wy){
    if(this.paused||this.gameOver)return;
    const cost=300;
    if(this.pGold<cost){showToast('💸 Topçu için yeterli altın yok! 300₺','#ff4444');return;}
    this.pGold-=cost;
    // Nişan animasyonu
    this.spawnFloatText(wx,wy-40,'🎯 NİŞANLANDI','#ffaa44');
    this.particles.push({x:wx,y:wy,r:30,life:300,maxLife:300,type:'ring'});
    // 1.5s gecikmeli mermi düşüşü
    this.time.delayedCall(1500,()=>{
      if(this.gameOver)return;
      const bx=wx+Phaser.Math.Between(-25,25);
      this.particles.push({x:bx,y:GND-10,r:70,life:700,maxLife:700,type:'exp'});
      this.particles.push({x:bx,y:GND-10,r:110,life:450,maxLife:450,type:'ring'});
      this.spawnExplosionSparks(bx,GND-10);
      this.cameras.main.shake(200,0.01,false);
      SFX.play('bigboom');
      const dmg=180+this.pAge*60;
      this.units.filter(u=>u.alive&&u.side==='e'&&Math.abs(u.x-bx)<70).forEach(u=>this.applyDamage(u,dmg,null));
      this._addLog('🎯 Topçu nişan isabeti: '+bx.toFixed(0)+'px');
    });
    // Tek atış sonrası mod kapanmaz — devam eder
    this.updateHUD();
  }

  // ── DALGA ÖNİZLEME ───────────────────────
  _showWavePreview(){
    const pool=REG.filter(d=>d.s==='e'&&d.a<=this.eAge);
    if(!pool.length)return;
    const waveSize=Math.min(6,2+this.eAge+Math.floor(this.waveCount/3));
    const typeCount={inf:0,veh:0,air:0};
    for(let i=0;i<waveSize;i++){
      const aff=pool.filter(d=>d.c<=(this.aiGold||800));
      const pick=aff.length?aff[Math.floor(Math.random()*aff.length)]:pool[0];
      typeCount[pick.t]=(typeCount[pick.t]||0)+1;
    }
    const units=Object.entries(typeCount).filter(([,v])=>v>0).map(([k,v])=>{
      const icons={inf:'🪖',veh:'🚛',air:'✈'};
      return icons[k]+' '+v+'×';
    }).join('  ');
    const wp=document.getElementById('wave-preview');
    const wn=document.getElementById('wp-wave-num');
    const wu=document.getElementById('wp-units-desc');
    if(wp&&wn&&wu){
      wn.textContent='DALGA '+(this.waveCount+1);
      wu.textContent=waveSize+' birim: '+units;
      wp.classList.add('show');
      // 5s geri sayım bar
      let elapsed=0;const barEl=document.getElementById('wp-bar');
      const tick=setInterval(()=>{elapsed+=100;const pct=Math.max(0,100-(elapsed/5000)*100);if(barEl)barEl.style.width=pct+'%';if(elapsed>=5000){clearInterval(tick);wp.classList.remove('show');}},100);
    }
    SFX.play('spawn');
  }

  // ── HAVADURumu DÖNGÜSÜ ───────────────────
  _updateWeather(dt){
    this.weatherTimer+=dt;
    if(this.weatherTimer>=this.weatherInterval){
      this.weatherTimer=0;
      this.weatherIdx=(this.weatherIdx+1)%this.weatherStates.length;
      this.curWeather=this.weatherStates[this.weatherIdx];
      const wMsg={clear:'☀ Hava açıldı! Görüş tam',rain:'🌧 Yağmur başladı! Ateş hızı -%10',cloudy:'⛅ Bulutlu hava'};
      showToast(wMsg[this.curWeather]||'Hava değişti','#88aacc');
      this._addLog(wMsg[this.curWeather]||'Hava değişti');
      // Yağmur yavaşlatması uygula
      if(this.curWeather==='rain'){
        this.units.forEach(u=>{if(!u.def.air)u._rainSlow=this.weatherInterval;});
      }else{
        this.units.forEach(u=>u._rainSlow=0);
      }
      this.bgDirty=true;this.drawBG();
    }
    // Yağmur damlası partikülleri
    if(this.curWeather==='rain'){
      if(!this._rainSpawnT)this._rainSpawnT=0;
      this._rainSpawnT+=dt;
      if(this._rainSpawnT>60){
        this._rainSpawnT=0;
        for(let i=0;i<4;i++){
          this.particles.push({x:Math.random()*W,y:-10,vx:-20+Math.random()*10,vy:280+Math.random()*120,life:600,maxLife:600,type:'rain'});
        }
      }
    }
    // Yağmur yavaşlamasını geri say
    this.units.forEach(u=>{if(u._rainSlow>0)u._rainSlow-=dt;});
  }

  // ── ALTIN SANDIKLARI ─────────────────────
  _updateGoldCrates(dt){
    this.crateTimer+=dt;
    if(this.crateTimer>=this.crateInterval){
      this.crateTimer=0;
      // 2 sandık spawn: biri haritanın sol, biri sağ yarısında
      const cx1=PBX+80+Math.random()*(W/2-PBX-100);
      const cx2=W/2+Math.random()*(EBX-W/2-80);
      [cx1,cx2].forEach(cx=>{
        const crate={x:cx,y:GND-14,alive:true,glow:0};
        this.goldCrates.push(crate);
        this.spawnFloatText(cx,GND-30,'💰','#f5a623');
        this.time.delayedCall(12000,()=>{crate.alive=false;});
      });
    }
    // Çizim + toplama
    const g=this.fxGfx;
    this.goldCrates=this.goldCrates.filter(c=>{
      if(!c.alive)return false;
      c.glow=(c.glow+dt*.004)%(Math.PI*2);
      const a=.7+Math.sin(c.glow)*.3;
      g.fillStyle(0xf5a623,a);g.fillRect(c.x-8,c.y-8,16,16);
      g.lineStyle(2,0xffdd00,a);g.strokeRect(c.x-8,c.y-8,16,16);
      // Yakın oyuncu birimi toplar
      const collector=this.units.find(u=>u.alive&&u.side==='p'&&Math.abs(u.x-c.x)<40&&Math.abs(u.y-c.y)<30);
      if(collector){
        const reward=150+this.pAge*50;
        this.pGold+=reward;
        this.spawnFloatText(c.x,c.y-20,'+'+reward+'₺ 💰','#f5a623');
        SFX.play('spawn');
        this._addLog('💰 Altın sandığı toplandı: +'+reward+'₺');
        return false;
      }
      return true;
    });
  }

  // ── BAŞARIM SAYACI ───────────────────────
  _updateAchievCount(){
    const el=document.getElementById('ach-count');
    if(el)el.textContent='🏅 '+this.achievements.size+'/13 Başarım';
  }

  // ── DALGA UYARISI ────────────────────────
  _showWaveAlert(){
    const wa=document.getElementById('wave-alert');if(!wa)return;
    wa.style.opacity='1';setTimeout(()=>{wa.style.opacity='0';},2000);
    this.cameras.main.flash(150,255,50,50,false);
  }

  // ── ÇAĞ SİNEMATİĞİ ──────────────────────
  _showAgeTransition(ageName){
    const existing=document.getElementById('age-transition');
    if(existing)existing.remove();
    const d=document.createElement('div');d.id='age-transition';
    Object.assign(d.style,{position:'absolute',top:'35%',left:'50%',transform:'translateX(-50%)',zIndex:'70',
      fontSize:'38px',fontWeight:'900',color:'#f5a623',textShadow:'0 0 30px #f39c12',
      letterSpacing:'6px',pointerEvents:'none',opacity:'1',transition:'opacity 1s',textAlign:'center'});
    d.innerHTML='⬆ '+ageName+'<br><span style="font-size:16px;color:#aaa;letter-spacing:2px">YENİ ÇAĞ BAŞLADI</span>';
    document.getElementById('root').appendChild(d);
    setTimeout(()=>{d.style.opacity='0';setTimeout(()=>d.remove(),1100);},2200);
  }

  // ── APPLYBASEDAMAGE YARDIMCISI ────────────
  applyBaseDamage(base,dmg){
    base.hp=Math.max(0,base.hp-dmg);
    // Oyuncu üssü hasar alırsa sarsıntı + uyarı
    if(base===this.pBase&&dmg>0){
      const intensity=Math.min(0.018,0.004+dmg/2000);
      this.cameras.main.shake(180,intensity,false);
      const pct=base.hp/base.maxHP;
      if(pct<0.3&&!this._lowHpWarned){
        this._lowHpWarned=true;
        showToast('⚠️ ÜS KRİTİK HASAR ALIYOR! %'+Math.ceil(pct*100)+' HP','#ff2222');
        this.cameras.main.flash(400,255,0,0,false);
      }
      if(pct>=0.3)this._lowHpWarned=false;
    }
    if(base.hp<=0&&!this.gameOver){
      this.endGame(base===this.eBase);
    }
  }

  // ── TARİHİ OLAYLAR ───────────────────────
  _triggerHistoricalEvent(){
    const events=[
      {icon:'🛢',title:'Petrol Bulgusu',desc:'Bölgede petrol keşfedildi, ekonomi canlandı.',effect:'+500₺ ve +%20 gelir (30s)',fn:()=>{this.pGold+=500;this.waveIncomeBoost=Math.max(this.waveIncomeBoost,30000);},col:'#f5a623'},
      {icon:'🌩',title:'Fırtına',desc:'Şiddetli hava koşulları hava birimlerini yavaşlattı.',effect:'Hava birimleri %30 yavaş (15s)',fn:()=>{this.units.filter(u=>u.alive&&u.def.air).forEach(u=>u._stormSlow=15000);},col:'#88aaff'},
      {icon:'🤝',title:'Müttefik Yardımı',desc:'NATO müttefikleri destek gönderiyor.',effect:'+2 takviye birimi, +300₺',fn:()=>{this.pGold+=300;this._sendReinforcements();this._sendReinforcements();},col:'#44ee88'},
      {icon:'📡',title:'İstihbarat Raporu',desc:'Düşman mevzileri tespit edildi!',effect:'Düşman %25 yavaş (12s)',fn:()=>{this.units.filter(u=>u.alive&&u.side==='e').forEach(u=>u._intelSlow=12000);},col:'#ffdd44'},
      {icon:'💣',title:'Cephane İkmali',desc:'Tüm birimler yeniden mühimmatlandı.',effect:'Tüm birimi saldırı hızı +%25 (20s)',fn:()=>{this.units.filter(u=>u.alive&&u.side==='p').forEach(u=>u._ammoBoost=20000);},col:'#ff8844'},
      {icon:'🏥',title:'Saha Hastanesi',desc:'Tüm yaralı birimler acil tedavi gördü.',effect:'Tüm dostlar %30 HP yenilendi',fn:()=>{this.units.filter(u=>u.alive&&u.side==='p').forEach(u=>{const h=Math.ceil(u.maxHP*.3);u.hp=Math.min(u.maxHP,u.hp+h);this.spawnFloatText(u.x,u.y-20,'+'+h,'#44ee88');});this.pBase.hp=Math.min(this.pBase.maxHP,this.pBase.hp+this.pBase.maxHP*.08);},col:'#44ee88'},
      {icon:'🎯',title:'Nişancı Tatbikatı',desc:'Komandolar keskinleşti.',effect:'Keskin nişancılar +%50 hasar (25s)',fn:()=>{this.units.filter(u=>u.alive&&u.side==='p'&&u.def.id&&u.def.id.includes('snp')).forEach(u=>u._sharpBoost=25000);},col:'#ff4444'},
      {icon:'🌊',title:'Deniz Desteği',desc:'Deniz kuvvetleri sahil topçusu açtı!',effect:'Düşman hatlarına 3 topçu isabet',fn:()=>{for(let i=0;i<3;i++)this.time.delayedCall(i*600,()=>{if(this.gameOver)return;const bx=EBX-50-Math.random()*400;this.particles.push({x:bx,y:GND-15,r:55,life:600,maxLife:600,type:'exp'});this.particles.push({x:bx,y:GND-15,r:85,life:350,maxLife:350,type:'ring'});this.spawnExplosionSparks(bx,GND-15);this.cameras.main.shake(120,0.008);SFX.play('bigboom');this.units.filter(u=>u.alive&&u.side==='e'&&Math.abs(u.x-bx)<55).forEach(u=>this.applyDamage(u,250+this.pAge*60));});},col:'#44aaff'},
      {icon:'⚡',title:'EMP Saldırısı',desc:'Düşman elektronik sistemleri çöktü!',effect:'Düşman tüm birimleri 10s devre dışı',fn:()=>{this.units.filter(u=>u.alive&&u.side==='e').forEach(u=>u._stunned=10000);this.cameras.main.flash(300,100,150,255,false);SFX.play('ult');},col:'#44aaff'},
      {icon:'🌙',title:'Gece Baskını',desc:'TSK özel kuvvetleri gece operasyonu başlattı.',effect:'Tüm düşmanlara %20 hasar, +200₺',fn:()=>{this.pGold+=200;this.units.filter(u=>u.alive&&u.side==='e').forEach(u=>this.applyDamage(u,Math.ceil(u.hp*.2)));},col:'#8888ff'},
    ];
    const ev=events[Math.floor(Math.random()*events.length)];
    ev.fn();
    this._addLog(ev.icon+' '+ev.title+': '+ev.effect);
    SFX.play('quiz_ok');
    // Kart göster
    const card=document.getElementById('event-card');
    const ei=document.getElementById('event-icon');
    const et=document.getElementById('event-title');
    const ed=document.getElementById('event-desc');
    const ee=document.getElementById('event-effect');
    if(card){
      if(ei)ei.textContent=ev.icon;
      if(et){et.textContent=ev.title;et.style.color=ev.col||'#f5a623';}
      if(ed)ed.textContent=ev.desc;
      if(ee){ee.textContent=ev.effect;ee.style.color=ev.col||'#44ee88';}
      card.classList.add('show');
      setTimeout(()=>card.classList.remove('show'),4500);
    }
    this._checkAchievements();
  }

  // ── KİLL FEED ────────────────────────────
  _addKillFeed(killerName,victimName,side){
    const feed=document.getElementById('kill-feed');if(!feed)return;
    const el=document.createElement('div');
    el.className='kf-entry '+(side==='p'?'p-kill':'e-kill');
    el.textContent=(side==='p'?'⚔ ':'💀 ')+killerName+' → '+victimName;
    feed.prepend(el);
    setTimeout(()=>{el.style.opacity='0';setTimeout(()=>el.remove(),1000);},5000);
    while(feed.children.length>8)feed.removeChild(feed.lastChild);
  }

  // ── TARET YÜKSELTMESİ ───────────────────
  upgradeTurret(){
    if(this.paused||this.gameOver)return;
    const maxLvl=3;
    if(this.turretUpgLevel>=maxLvl){showToast('Kule zaten maksimum seviyede!','#ffaa44');return;}
    const cost=600+this.turretUpgLevel*400;
    if(this.pGold<cost){showToast('Yetersiz altın! '+cost+'₺ gerekli','#ff4444');return;}
    this.pGold-=cost;this.turretUpgLevel++;
    const lvl=this.turretUpgLevel;
    this.pTurrets.filter(t=>t.alive).forEach(t=>{
      t.dmg=Math.ceil(t.dmg*1.25);
      t.rt=Math.ceil(t.rt*.85);
      if(lvl>=2)t.rng=Math.ceil(t.rng*1.1);
      if(lvl>=3){t.hp=Math.min(t.maxHP,t.hp+Math.ceil(t.maxHP*.3));t.maxHP=Math.ceil(t.maxHP*1.2);}
    });
    const icons=['','⭐','⭐⭐','⭐⭐⭐'];
    showToast('🏰 Kule Seviye '+lvl+' '+icons[lvl]+'! Hasar +%25, Hız +%15'+(lvl>=2?' Menzil +%10':''),'#44aaff');
    this._addLog('🏰 Kule Yükseltme Seviye '+lvl);
    SFX.play('upgrade');this.cameras.main.flash(250,0,100,200,false);
    this.updateHUD();
  }

  // ── KAMPANYA ────────────────────────────
  continueCampaign(){
    const nextMission=this.campaignMission+1;
    if(nextMission>=CAMPAIGN_MISSIONS.length)return;
    const m=CAMPAIGN_MISSIONS[nextMission];
    window._campaignMission=nextMission;
    window._selectedDiff=m.diff;
    window._missionBonus=m.bonus;
    window._isCampaign=true;
    document.getElementById('endscreen').classList.remove('show');
    // Kampanya ekranı göster
    const cs=document.getElementById('campaign-screen');
    document.getElementById('camp-num').textContent='GÖREV '+(nextMission+1)+' / '+CAMPAIGN_MISSIONS.length;
    document.getElementById('camp-title').textContent=m.title;
    document.getElementById('camp-desc').textContent=m.desc;
    cs.classList.add('open');
  }
}

// ═══════════════════════════════════════════
//  HELPERS
// ═══════════════════════════════════════════
function showToast(msg,color='#fff'){
  const d=document.createElement('div');
  d.className='toast';d.textContent=msg;d.style.color=color;
  document.getElementById('toasts').appendChild(d);
  setTimeout(()=>d.remove(),2950);
}

// ═══════════════════════════════════════════
//  PHASER CONFIG
// ═══════════════════════════════════════════
const config={
  type:Phaser.AUTO,
  width:W,height:H,
  backgroundColor:'#0a0e18',
  parent:'root',
  scene:[GameScene],
  audio:{noAudio:true},
  render:{antialias:true,pixelArt:false,roundPixels:false,powerPreference:'high-performance'}
};

// Oyun, zorluk seçiminden sonra başlar
let game=null;

function _launchGame(){
  if(!game)game=new Phaser.Game(config);
}

// Alt-tab otomatik duraklat
document.addEventListener('visibilitychange',()=>{
  if(document.hidden&&game&&game.scene.scenes[0]){
    const sc=game.scene.scenes[0];
    if(!sc.gameOver&&!sc.paused){
      sc.paused=true;
      const pm=document.getElementById('pause-menu');
      if(pm)pm.classList.add('open');
    }
  }
});

// Zorluk seçici
window._startGame=function(diff){
  window._selectedDiff=diff;
  window._missionBonus=0;
  window._campaignMission=0;
  window._isCampaign=false;
  document.getElementById('diff-screen').style.display='none';
  _launchGame();
};

// Kampanya başlat
window._startCampaign=function(){
  const m=CAMPAIGN_MISSIONS[0];
  window._selectedDiff=m.diff;
  window._missionBonus=m.bonus;
  window._campaignMission=0;
  window._isCampaign=true;
  document.getElementById('diff-screen').style.display='none';
  const cs=document.getElementById('campaign-screen');
  document.getElementById('camp-num').textContent='GÖREV 1 / '+CAMPAIGN_MISSIONS.length;
  document.getElementById('camp-title').textContent=m.title;
  document.getElementById('camp-desc').textContent=m.desc;
  cs.classList.add('open');
};

// Kampanya görevi başlat
window._beginCampaignMission=function(){
  document.getElementById('campaign-screen').classList.remove('open');
  _launchGame();
  // Eğer oyun zaten varsa yeniden başlat
  if(game){game.destroy(true);game=null;setTimeout(()=>{game=new Phaser.Game(config);},50);}
};

// Yüksek skorları diff-screen'de göster (oyun başlamadan)
(function(){
  try{
    const scores=JSON.parse(localStorage.getItem('cgbs_scores'))||[];
    const el=document.getElementById('hi-scores-list');
    if(!el)return;
    if(!scores.length){el.innerHTML='<div style="color:#555;font-size:11px">Henüz kayıt yok</div>';return;}
    el.innerHTML='<div style="color:#888;font-size:10px;margin-bottom:4px;letter-spacing:2px">YÜK. SKORLAR</div>'+
      scores.slice(0,5).map((s,i)=>`<div style="font-size:10px">${['🥇','🥈','🥉','4.','5.'][i]} <span style="color:#f39c12;font-weight:700">${s.score.toLocaleString()}</span> — ${DIFFICULTY[s.diff].label} — ${s.kills} imha — ${s.date}</div>`).join('');
  }catch(e){}
})();
</script>
</body>
</html>
