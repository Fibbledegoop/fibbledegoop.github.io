
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Peak Season: The Card Game</title>
<style>
  :root{
    --navy:#0b1929; --navy2:#11263d; --panel:#16304d; --panel2:#1d3d61;
    --orange:#ff9900; --orange2:#ffb84d; --cyan:#33ccff; --purple:#a56bff;
    --green:#4ade80; --red:#ff5c5c; --gold:#ffd24d; --ink:#e8f0fa; --muted:#9fb4cc;
    --shadow:0 8px 30px rgba(0,0,0,.5);
  }
  *{box-sizing:border-box;margin:0;padding:0}
  html,body{height:100%;overflow:hidden;max-width:100%}
  html{position:fixed;inset:0}
  body{
    font-family:"Segoe UI",Roboto,system-ui,sans-serif;
    background:var(--navy); color:var(--ink); overflow:hidden;
    user-select:none; -webkit-user-select:none;
  }
  #app{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;overflow:hidden}
  .screen{position:absolute;inset:0;display:none;flex-direction:column;overflow:hidden}
  .screen.active{display:flex}
  button{font-family:inherit;cursor:pointer;border:none;color:var(--ink)}
  .btn{
    background:linear-gradient(180deg,var(--orange2),var(--orange));
    color:#241300;font-weight:700;padding:12px 26px;border-radius:10px;
    font-size:16px;box-shadow:var(--shadow);transition:transform .08s,filter .1s;
    border:2px solid rgba(255,255,255,.15);
  }
  .btn:hover{filter:brightness(1.08);transform:translateY(-2px)}
  .btn:active{transform:translateY(0)}
  .btn.secondary{background:linear-gradient(180deg,var(--panel2),var(--panel));color:var(--ink)}
  .btn.ghost{background:transparent;border:2px solid var(--panel2);color:var(--muted);box-shadow:none}
  .btn:disabled{filter:grayscale(.7) brightness(.6);cursor:not-allowed;transform:none}

  /* ---------- TITLE ---------- */
  #title{background-size:cover;background-position:center;align-items:center;justify-content:flex-start;text-align:center;overflow-y:auto;overflow-x:hidden}
  #title .overlay{position:fixed;inset:0;background:linear-gradient(180deg,rgba(5,12,22,.55),rgba(5,12,22,.85));z-index:1}
  #title .content{position:relative;z-index:2;display:flex;flex-direction:column;align-items:center;gap:8px;padding:24px 20px 40px;margin:auto;min-height:min-content}
  #title h1{font-size:clamp(38px,7vw,84px);line-height:.95;text-shadow:0 4px 24px #000, 0 0 40px rgba(255,153,0,.4);letter-spacing:1px}
  #title h1 .a{color:var(--orange)}
  #title .tag{color:var(--orange2);font-size:clamp(15px,2.4vw,22px);font-weight:600;margin-bottom:4px;text-shadow:0 2px 10px #000}
  #title .sub{color:var(--muted);max-width:640px;font-size:15px;line-height:1.5;margin-bottom:14px;text-shadow:0 2px 8px #000}
  .classpick{display:flex;gap:26px;margin-top:14px;flex-wrap:wrap;justify-content:center}
  .classcard{width:250px;background:rgba(11,25,41,.82);border:2px solid var(--panel2);border-radius:16px;overflow:hidden;transition:transform .12s,border-color .12s,box-shadow .12s;box-shadow:var(--shadow)}
  .classcard:hover{transform:translateY(-6px);border-color:var(--orange);box-shadow:0 12px 40px rgba(255,153,0,.3)}
  .classcard.picked{border-color:var(--green);box-shadow:0 0 0 3px var(--green),0 12px 40px rgba(74,222,128,.4)}
  .classcard{position:relative}
  .diffbtn{background:var(--panel);border:2px solid var(--panel2);color:var(--muted);padding:6px 14px;border-radius:8px;font-weight:700;font-size:13px;cursor:pointer;transition:all .1s}
  .diffbtn:hover{border-color:var(--orange)}
  .diffbtn.sel{background:linear-gradient(180deg,var(--orange2),var(--orange));color:#241300;border-color:#fff3d6}
  .pickbadge:not(:empty){position:absolute;top:8px;right:8px;width:28px;height:28px;border-radius:50%;background:var(--green);color:#04210f;font-weight:900;display:flex;align-items:center;justify-content:center;font-size:15px;border:2px solid #fff;box-shadow:0 2px 8px #000;z-index:3}
  .classcard img{width:100%;height:230px;object-fit:cover;object-position:center top;display:block}
  .classcard .info{padding:12px 14px 16px}
  .classcard h3{font-size:20px;margin-bottom:6px}
  .classcard .role{display:inline-block;font-size:11px;font-weight:700;padding:2px 8px;border-radius:20px;margin-bottom:8px}
  .classcard p{font-size:12.5px;color:var(--muted);line-height:1.5;min-height:56px}
  .classcard .stat{font-size:12px;color:var(--cyan);margin-top:8px}
  .role.ops{background:rgba(255,153,0,.2);color:var(--orange2)}
  .role.aws{background:rgba(51,204,255,.2);color:var(--cyan)}
  .role.pxt{background:rgba(74,222,128,.2);color:var(--green)}
  .role.lnd{background:rgba(165,107,255,.2);color:var(--purple)}
  .role.lastmile{background:rgba(51,204,255,.2);color:var(--cyan)}
  .role.legal{background:rgba(255,80,80,.2);color:#ff8a8a}
  .role.finance{background:rgba(74,222,128,.2);color:#ffd24d}
  .role.security{background:rgba(74,222,128,.2);color:#8fffbf}

  /* ---------- MAP ---------- */
  #map{background:linear-gradient(rgba(6,15,26,.72),rgba(6,15,26,.82)),url('assets/bg_map.png');background-size:cover;background-position:center;background-attachment:fixed}
  .topbar{display:flex;align-items:center;gap:18px;padding:12px 20px;background:rgba(6,15,26,.7);border-bottom:2px solid var(--panel2);z-index:5;flex-wrap:wrap}
  .topbar .who{display:flex;align-items:center;gap:10px;font-weight:700}
  .topbar .who img{width:38px;height:38px;border-radius:50%;object-fit:cover;object-position:center top;border:2px solid var(--orange)}
  .pill{display:flex;align-items:center;gap:6px;background:var(--panel);padding:6px 12px;border-radius:20px;font-size:14px;font-weight:700}
  .pill.hp{color:var(--green)} .pill.gold{color:var(--gold)} .pill.floor{color:var(--cyan)}
  .hpbar{width:120px;height:9px;background:#2a1414;border-radius:5px;overflow:hidden;border:1px solid #000}
  .hpbar > i{display:block;height:100%;background:linear-gradient(90deg,#ff5c5c,#4ade80);transition:width .3s}
  .topbar .spacer{flex:1}
  .relictray{display:flex;gap:6px;flex-wrap:wrap;max-width:340px}
  .potionbelt{display:flex;gap:5px}
  .potionslot{width:34px;height:34px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:17px;position:relative;border:2px solid var(--panel2);background:var(--panel)}
  .potionslot.filled{border-color:var(--green);cursor:pointer;background:radial-gradient(circle at 40% 30%,rgba(74,222,128,.35),var(--panel));box-shadow:0 0 10px rgba(74,222,128,.4)}
  .potionslot.filled:hover{transform:translateY(-2px);box-shadow:0 0 16px rgba(74,222,128,.7)}
  .potionslot.empty{color:#3c5a7c;font-weight:700}
  .potionslot:hover .tip{display:block}
  .relic{width:34px;height:34px;border-radius:8px;background:var(--panel2);display:flex;align-items:center;justify-content:center;font-size:18px;border:1px solid var(--gold);position:relative}
  .relic:hover .tip{display:block}
  .tip{display:none;position:absolute;top:40px;left:50%;transform:translateX(-50%);background:#050c16;border:1px solid var(--orange);padding:8px 10px;border-radius:8px;width:190px;font-size:11.5px;color:var(--ink);z-index:999;box-shadow:var(--shadow);font-weight:400;line-height:1.4;text-align:left}
  .tip b{color:var(--gold)}
  #mapScroll{flex:1;overflow-y:auto;overflow-x:hidden;padding:20px 0 40px}
  #mapCanvas{position:relative;margin:0 auto}
  .node{position:absolute;width:96px;height:96px;border-radius:16px;display:flex;align-items:center;justify-content:center;font-size:26px;transform:translate(-50%,-50%);border:3px solid #3a5f8a;background:rgba(10,22,37,.6);background-size:cover;background-position:center;background-repeat:no-repeat;box-shadow:0 4px 18px rgba(0,0,0,.6);transition:transform .12s,box-shadow .12s,border-color .12s;z-index:3}
  .node.reachable{cursor:pointer;border-color:var(--orange);box-shadow:0 0 18px rgba(255,153,0,.5);animation:pulse 1.6s infinite}
  .node.reachable:hover{transform:translate(-50%,-50%) scale(1.14)}
  .node.done{opacity:.42;filter:grayscale(.4)}
  .node.current{border-color:var(--cyan);box-shadow:0 0 22px var(--cyan)}
  @keyframes pulse{0%,100%{box-shadow:0 0 12px rgba(255,153,0,.4)}50%{box-shadow:0 0 26px rgba(255,153,0,.85)}}
  svg.edges{position:absolute;inset:0;z-index:1;pointer-events:none}
  .edge{stroke:#2c4d74;stroke-width:3;fill:none}
  .edge.open{stroke:var(--orange);stroke-dasharray:6 6;opacity:.8}
  .maplabel{position:absolute;transform:translate(-50%,-50%);font-size:10px;color:var(--muted);margin-top:38px;white-space:nowrap;pointer-events:none}
  #mapLegend{display:flex;flex-wrap:wrap;align-items:center;gap:8px 14px;padding:8px 20px;background:rgba(6,15,26,.55);border-bottom:1px solid var(--panel2);z-index:4;font-size:12px}
  #mapLegend .legendhint{color:var(--muted);font-style:italic;margin-right:2px}
  .legenditem{position:relative;display:flex;align-items:center;gap:5px;color:var(--ink);background:var(--panel);padding:4px 10px;border-radius:16px;cursor:default;border:1px solid var(--panel2)}
  .legenditem b{font-size:15px}
  .legenditem:hover{border-color:var(--orange)}
  .legenditem .legendtip{display:none;position:absolute;top:34px;left:0;z-index:999;background:#050c16;border:1px solid var(--orange);border-radius:8px;padding:8px 10px;width:240px;font-size:11.5px;line-height:1.4;color:var(--ink);box-shadow:var(--shadow);font-style:normal}
  .legenditem:hover .legendtip{display:block}

  /* ---------- COMBAT ---------- */
  #combat{background-size:cover;background-position:center;position:relative}
  #combat .cbveil{position:absolute;inset:0;background:linear-gradient(180deg,rgba(6,14,24,.35),rgba(6,14,24,.78))}
  #combat .cbtop{position:relative;z-index:3}
  .arena{position:relative;z-index:2;flex:1;display:flex;justify-content:space-between;align-items:flex-start;padding:26px 46px 6px}
  .side{display:flex;gap:24px;align-items:flex-start}
  .enemies{gap:34px}
  .fighter{display:flex;flex-direction:column;align-items:center;gap:8px;position:relative;transition:transform .1s}
  .fighter img{width:150px;height:150px;object-fit:contain;filter:drop-shadow(0 10px 18px rgba(0,0,0,.6));transition:transform .12s,filter .12s}
  .enemy .fighter-inner{cursor:pointer}
  .enemy.targetable img:hover{transform:scale(1.06);filter:drop-shadow(0 0 16px var(--red))}
  .enemy.targetable{outline:2px dashed rgba(255,92,92,.6);outline-offset:6px;border-radius:12px}
  .enemy.drophover img{transform:scale(1.12);filter:drop-shadow(0 0 22px var(--red)) brightness(1.2)}
  .enemy.drophover{outline:3px solid var(--red);outline-offset:6px;border-radius:12px}
  .fighter.hero img{width:172px;height:172px}
  .fighter.hero{cursor:pointer;border-radius:14px;padding:4px;transition:box-shadow .15s,transform .12s;position:relative}
  .fighter.hero.active-ally{box-shadow:0 0 0 3px var(--active,var(--orange)),0 0 22px var(--active,rgba(255,153,0,.5));background:rgba(255,255,255,.07)}
  .fighter.hero.targeted-ally{outline:3px dashed #ff5c5c;outline-offset:5px;border-radius:14px;animation:tgtpulse 1s ease-in-out infinite}
  @keyframes tgtpulse{0%,100%{outline-color:rgba(255,92,92,.5)}50%{outline-color:#ff2020}}
  .target-banner{position:absolute;bottom:-16px;left:50%;transform:translateX(-50%);background:#ff2020;color:#fff;font-weight:800;font-size:12px;padding:3px 12px;border-radius:12px;border:2px solid #fff;white-space:nowrap;z-index:14;box-shadow:0 2px 10px rgba(0,0,0,.6)}
  .target-banner b{font-size:14px}
  .fighter.hero.active-ally img{width:180px;height:180px}
  .fighter.hero.downed{filter:grayscale(1) brightness(.5);opacity:.6;cursor:default}
  .fighter.hero.downed::after{content:'DOWN';position:absolute;top:50%;left:50%;transform:translate(-50%,-50%) rotate(-12deg);background:#7a1414;color:#fff;font-weight:900;font-size:14px;padding:3px 10px;border-radius:6px;border:2px solid #ff5c5c;z-index:6}
  .fighter.hero:not(.active-ally):not(.downed):hover{box-shadow:0 0 0 2px var(--cyan)}
  .fighter.enemy.targeted::before{content:'🎯';position:absolute;top:-34px;left:50%;transform:translateX(-50%);font-size:18px;z-index:6}
  
  .hitflash{animation:hitflash .3s}
  @keyframes hitflash{0%,100%{filter:none}30%{filter:brightness(2.2) sepia(1) hue-rotate(-30deg) saturate(4)}}
  .shake{animation:shake .3s}
  @keyframes shake{0%,100%{transform:translateX(0)}25%{transform:translateX(-8px)}75%{transform:translateX(8px)}}
  .namep{font-weight:700;font-size:14px;text-shadow:0 2px 6px #000;text-align:center}
  .barwrap{width:150px}
  .ehp{height:14px;background:#2a1414;border-radius:7px;overflow:hidden;border:1px solid #000;position:relative}
  .ehp > i{display:block;height:100%;background:linear-gradient(90deg,#ff5c5c,#ff9090);transition:width .3s}
  .ehp span{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;font-size:11px;font-weight:700;text-shadow:0 1px 2px #000}
  .block-badge{position:absolute;top:-6px;left:-6px;background:#6ab0ff;color:#041225;border-radius:8px;padding:2px 7px;font-size:12px;font-weight:800;border:2px solid #cde6ff;display:flex;align-items:center;gap:2px;z-index:4;box-shadow:0 2px 6px #000}
  .intent{position:absolute;top:-26px;left:50%;transform:translateX(-50%);background:rgba(5,12,22,.9);border:1px solid var(--red);border-radius:8px;padding:4px 10px;font-size:13px;font-weight:800;cursor:help;z-index:7;white-space:nowrap;z-index:5;box-shadow:0 2px 8px #000}
  .intent.def{border-color:var(--cyan)} .intent.buf{border-color:var(--purple)} .intent.dbf{border-color:var(--orange)}
  .statuses{display:flex;gap:5px;flex-wrap:wrap;justify-content:center;margin-top:2px;max-width:160px}
  .tokentray{display:flex;gap:6px;flex-wrap:wrap;justify-content:center;margin-top:8px;max-width:200px}
  .token{background:rgba(6,14,24,.7);border:2px solid var(--cyan);border-radius:8px;padding:2px 6px;font-size:18px;display:flex;flex-direction:column;align-items:center;line-height:1;animation:fadein .3s}
  .token.package{border-color:#6ab0ff}
  .token .tv{font-size:9px;font-weight:800;margin-top:1px;color:var(--ink)}
  .status{background:var(--panel2);border:1px solid #3c6494;border-radius:7px;padding:1px 6px;font-size:11px;font-weight:700;display:flex;gap:3px;align-items:center;position:relative;cursor:default}
  .status.buff{border-color:var(--green)} .status.debuff{border-color:var(--red)}
  .status:hover{z-index:1000}
  .status:hover .tip{display:block}
  .intent:hover .tip.intent-tip{display:block}
  .tip.intent-tip{top:32px;bottom:auto;width:180px;border-color:var(--red)}
  .fighter:hover{z-index:60}
  .arena:has(.status:hover){z-index:900}
  .float{position:absolute;font-weight:900;font-size:26px;pointer-events:none;z-index:20;text-shadow:0 2px 6px #000, 0 0 10px currentColor;animation:floatUp 1s ease-out forwards}
  .float.dmg{color:#ff6b6b} .float.blk{color:#7cc3ff} .float.heal{color:#7dffa0} .float.buf{color:#c79bff}
  @keyframes floatUp{0%{opacity:0;transform:translateY(0) scale(.6)}15%{opacity:1;transform:translateY(-10px) scale(1.15)}100%{opacity:0;transform:translateY(-70px) scale(1)}}
  /* card-play feedback */
  .cardfly{position:absolute;z-index:80;pointer-events:none;border-radius:14px;overflow:hidden;
    transition:transform .34s cubic-bezier(.4,.9,.3,1),opacity .34s ease-in;
    box-shadow:0 14px 44px rgba(0,0,0,.75),0 0 22px rgba(255,153,0,.5)}
  .cardfly .card{width:100%!important;height:100%!important;margin:0!important;transform:none!important}
  .fighter.lunge{animation:lunge .42s ease}
  @keyframes lunge{0%{transform:translateX(0)}38%{transform:translateX(64px)}100%{transform:translateX(0)}}
  .shieldpulse{position:absolute;left:50%;top:42%;transform:translate(-50%,-50%);font-size:46px;
    z-index:16;pointer-events:none;animation:shieldpulse .62s ease-out forwards;
    filter:drop-shadow(0 0 14px var(--cyan))}
  @keyframes shieldpulse{0%{opacity:0;transform:translate(-50%,-50%) scale(.35)}
    40%{opacity:1;transform:translate(-50%,-50%) scale(1.25)}
    100%{opacity:0;transform:translate(-50%,-50%) scale(1.85)}}
  .castring{position:absolute;left:50%;top:44%;width:60px;height:60px;transform:translate(-50%,-50%);
    border:3px solid var(--purple);border-radius:50%;z-index:15;pointer-events:none;
    animation:castring .5s ease-out forwards;box-shadow:0 0 18px var(--purple)}
  @keyframes castring{0%{opacity:.9;transform:translate(-50%,-50%) scale(.3)}100%{opacity:0;transform:translate(-50%,-50%) scale(2.2)}}
  /* hero-damage impact: screen shake + red vignette flash */
  #combat.screenshake{animation:screenshake .34s ease}
  @keyframes screenshake{0%,100%{transform:translate(0,0)}
    20%{transform:translate(-9px,4px)}40%{transform:translate(8px,-5px)}
    60%{transform:translate(-6px,-3px)}80%{transform:translate(5px,4px)}}
  #dmgVeil{position:fixed;inset:0;z-index:70;pointer-events:none;opacity:0;
    background:radial-gradient(ellipse at center,transparent 45%,rgba(255,30,30,.55) 100%)}
  #dmgVeil.flash{animation:dmgveil .5s ease-out}
  @keyframes dmgveil{0%{opacity:0}18%{opacity:1}100%{opacity:0}}
  /* draw animation: cards slide up from the draw pile */
  .card.dealt{animation:dealin .32s cubic-bezier(.2,.8,.3,1.1) backwards}
  @keyframes dealin{0%{opacity:0;transform:translateY(120px) scale(.6) rotate(-8deg)!important}
    100%{opacity:1}}
  /* particles */
  .particle{position:absolute;z-index:78;pointer-events:none;border-radius:50%;
    will-change:transform,opacity}
  .spark{width:7px;height:7px;background:radial-gradient(circle,#fff,#ff9a3c);
    box-shadow:0 0 8px #ff7a2c}
  .shard{width:8px;height:8px;background:radial-gradient(circle,#dff2ff,#5bd0ff);
    box-shadow:0 0 8px #5bd0ff;border-radius:2px}
  /* sound toggle button */
  #sfxToggle{padding:8px 12px;font-size:13px}
  /* combo counter */
  #comboPop{position:absolute;top:20%;left:50%;transform:translate(-50%,-50%) scale(1);
    z-index:85;pointer-events:none;text-align:center;opacity:0}
  #comboPop.show{animation:comboPop .7s cubic-bezier(.2,.8,.3,1.2)}
  @keyframes comboPop{0%{opacity:0;transform:translate(-50%,-50%) scale(.4)}
    25%{opacity:1;transform:translate(-50%,-50%) scale(1.15)}
    75%{opacity:1;transform:translate(-50%,-50%) scale(1)}
    100%{opacity:0;transform:translate(-50%,-50%) scale(1.05)}}
  #comboPop .cnum{font-size:52px;font-weight:900;color:var(--orange);
    text-shadow:0 3px 14px #000,0 0 26px rgba(255,153,0,.7);line-height:1}
  #comboPop .cmsg{font-size:20px;font-weight:800;color:var(--orange2);text-shadow:0 2px 8px #000;margin-top:2px}
  #comboPop.tier2 .cnum{color:var(--cyan);text-shadow:0 3px 14px #000,0 0 26px rgba(51,204,255,.8)}
  #comboPop.tier3 .cnum{color:var(--purple);text-shadow:0 3px 14px #000,0 0 30px rgba(165,107,255,.9)}
  /* enemy wind-up telegraph before it strikes */
  .fighter.under-attack{outline:5px solid #ff2020;outline-offset:6px;border-radius:14px;animation:targetpulse .6s ease-in-out infinite;position:relative;z-index:12;background:radial-gradient(circle at 50% 40%,rgba(255,30,30,.55),rgba(150,0,0,.35));transform:scale(1.08)}
  .fighter.under-attack img{filter:drop-shadow(0 0 22px #ff2020) brightness(1.15) saturate(1.4) !important}
  .fighter.under-attack .namep{color:#ff5c5c;font-weight:900}
  .fighter.under-attack::before{content:'';position:absolute;inset:-6px;border-radius:16px;background:rgba(255,30,30,.25);z-index:-1;animation:targetpulse .6s ease-in-out infinite}
  .fighter.under-attack::after{content:'🎯 UNDER ATTACK';position:absolute;bottom:-42px;left:50%;transform:translateX(-50%);background:#ff2020;color:#fff;font-weight:900;font-size:14px;padding:5px 14px;border-radius:10px;border:2px solid #fff;white-space:nowrap;z-index:13;box-shadow:0 3px 12px #000;animation:targetlabel .6s ease-in-out infinite}
  @keyframes targetpulse{0%,100%{outline-color:rgba(255,30,30,.6);box-shadow:0 0 16px rgba(255,30,30,.5)}50%{outline-color:#ff2020;box-shadow:0 0 40px rgba(255,30,30,1)}}
  @keyframes targetlabel{0%,100%{transform:translateX(-50%) scale(1)}50%{transform:translateX(-50%) scale(1.12)}}
  #combat.under-attack-veil::after{content:'';position:absolute;inset:0;z-index:1;pointer-events:none;background:radial-gradient(ellipse at center,transparent 55%,rgba(255,0,0,.22));animation:veilpulse .6s ease-in-out infinite}
  @keyframes veilpulse{0%,100%{opacity:.4}50%{opacity:.9}}
  .fighter.enemylunge img{animation:enemylunge .45s ease}
  @keyframes enemylunge{0%{transform:translateX(0)}40%{transform:translateX(-40px)}100%{transform:translateX(0)}}
  .fighter.windup img{animation:windup .5s ease}
  @keyframes windup{0%{transform:scale(1)}55%{transform:scale(1.14) translateX(-14px);filter:brightness(1.5) drop-shadow(0 0 16px var(--red))}100%{transform:scale(1)}}
  .fighter.windup .intent{animation:intentflare .5s ease}
  @keyframes intentflare{0%,100%{transform:translateX(-50%) scale(1)}50%{transform:translateX(-50%) scale(1.35);filter:drop-shadow(0 0 10px var(--red))}}
  /* music toggle */
  #musicToggle{padding:8px 12px;font-size:13px}

  /* hand + energy */
  .handdock{position:relative;z-index:4;padding:6px 16px 14px;display:flex;align-items:flex-end;gap:12px;width:100%;max-width:100%;min-width:0;overflow:visible}
  .energyorb{width:78px;height:78px;flex:none;border-radius:50%;background:radial-gradient(circle at 35% 30%,#ffd98a,var(--orange) 70%);display:flex;flex-direction:column;align-items:center;justify-content:center;color:#241300;font-weight:900;box-shadow:0 0 22px rgba(255,153,0,.6),inset 0 -6px 14px rgba(120,60,0,.5);border:3px solid #ffe4a8}
  .energyorb b{font-size:26px;line-height:1} .energyorb small{font-size:10px;font-weight:800;opacity:.8}
  .hand{flex:1;display:flex;justify-content:center;align-items:flex-end;gap:-20px;min-height:220px;perspective:1000px;overflow:visible;min-width:0}
  .card{width:150px;height:210px;flex:none;margin:0 -14px;border-radius:14px;position:relative;background:linear-gradient(180deg,var(--panel2),var(--panel));border:2px solid #34597f;box-shadow:var(--shadow);transition:transform .14s,margin .14s,box-shadow .14s;cursor:pointer;transform-origin:bottom center;display:flex;flex-direction:column;overflow:hidden}
  .hand .card:hover{transform:translateY(-40px) scale(1.18) rotate(0deg)!important;z-index:20;box-shadow:0 18px 44px rgba(0,0,0,.7),0 0 22px rgba(255,255,255,.35)}
  .hand .card.cls-generic:hover{box-shadow:0 18px 44px rgba(0,0,0,.7),0 0 24px #8fa3b8}
  .hand .card.cls-ops:hover{box-shadow:0 18px 44px rgba(0,0,0,.7),0 0 24px #ff9900}
  .hand .card.cls-aws:hover{box-shadow:0 18px 44px rgba(0,0,0,.7),0 0 24px #33ccff}
  .hand .card.cls-pxt:hover{box-shadow:0 18px 44px rgba(0,0,0,.7),0 0 24px #4ade80}
  .hand .card.cls-lnd:hover{box-shadow:0 18px 44px rgba(0,0,0,.7),0 0 24px #a56bff}
  .hand .card.cls-lastmile:hover{box-shadow:0 18px 44px rgba(0,0,0,.7),0 0 24px #2bb7e6}
  .hand .card.cls-legal:hover{box-shadow:0 18px 44px rgba(0,0,0,.7),0 0 24px #ff5c5c}
  .hand .card.cls-finance:hover{box-shadow:0 18px 44px rgba(0,0,0,.7),0 0 24px #ffd24d}
  .hand .card.cls-security:hover{box-shadow:0 18px 44px rgba(0,0,0,.7),0 0 24px #4ade80}
  .card.attack{border-color:#ff8a5c;background:linear-gradient(180deg,#7a2d18,#3f1710)}
  .card.skill{border-color:#4ec3ff;background:linear-gradient(180deg,#124a6e,#0a2438)}
  .card.power{border-color:#c79bff;background:linear-gradient(180deg,#4a2578,#25133f)}
  /* thick class-colored borders (override type border) */
  .card.cls-generic{border:4px solid #8fa3b8}
  .card.cls-ops{border:4px solid var(--orange)}
  .card.cls-aws{border:4px solid var(--cyan)}
  .card.cls-pxt{border:4px solid var(--green)}
  .card.cls-lnd{border:4px solid var(--purple)}
  .card.cls-lastmile{border:4px solid #2bb7e6}
  .card.cls-legal{border:4px solid #ff5c5c}
  .card.cls-finance{border:4px solid #ffd24d}
  .card.cls-security{border:4px solid #4ade80}
  /* cost circle tinted to class color */
  .card.cls-generic .cost{background:radial-gradient(circle at 40% 35%,#e8f0fa,#8fa3b8)}
  .card.cls-ops .cost{background:radial-gradient(circle at 40% 35%,#ffe4a8,#ff9900)}
  .card.cls-aws .cost{background:radial-gradient(circle at 40% 35%,#c9f2ff,#33ccff)}
  .card.cls-pxt .cost{background:radial-gradient(circle at 40% 35%,#c8f7d6,#4ade80)}
  .card.cls-lnd .cost{background:radial-gradient(circle at 40% 35%,#e0ccff,#a56bff)}
  .card.cls-lastmile .cost{background:radial-gradient(circle at 40% 35%,#c9f2ff,#2bb7e6)}
  .card.cls-legal .cost{background:radial-gradient(circle at 40% 35%,#ffd0d0,#ff5c5c)}
  .card.cls-finance .cost{background:radial-gradient(circle at 40% 35%,#fff0c2,#ffd24d)}
  .card.cls-security .cost{background:radial-gradient(circle at 40% 35%,#c8f7d6,#4ade80)}
  .card.unplayable{filter:grayscale(.6) brightness(.7)}
  .card .cost{position:absolute;top:-2px;left:-2px;width:34px;height:34px;background:radial-gradient(circle at 40% 35%,#ffe4a8,var(--orange));border-radius:50%;display:flex;align-items:center;justify-content:center;color:#241300;font-weight:900;font-size:17px;z-index:3;border:2px solid #fff3d6;box-shadow:0 2px 6px #000}
  .card .art{height:96px;background:#0c1c2f;display:flex;align-items:center;justify-content:center;font-size:38px;border-bottom:2px solid #34597f;text-shadow:0 2px 6px rgba(0,0,0,.85),0 0 10px rgba(0,0,0,.6)}
  .card .art.cardart{background-size:cover;background-position:center;background-repeat:no-repeat}
  .card.attack .art{background:linear-gradient(135deg,rgba(255,122,60,.55),rgba(194,48,22,.55)),url('assets/type_attack.png');background-size:cover;background-position:center}
  .card.skill .art{background:linear-gradient(135deg,rgba(46,166,230,.5),rgba(15,91,140,.5)),url('assets/type_skill.png');background-size:cover;background-position:center}
  .card.power .art{background:linear-gradient(135deg,rgba(154,92,255,.5),rgba(90,43,168,.5)),url('assets/type_power.png');background-size:cover;background-position:center}
  .card .cname{font-size:14px;font-weight:800;text-align:center;padding:6px 4px 2px;line-height:1.12}
  .card .ctype{font-size:9px;text-align:center;color:var(--muted);text-transform:uppercase;letter-spacing:.5px}
  .card .cbadge{font-size:9.5px;text-align:center;font-weight:800;margin:0;padding:3px 4px;border-radius:0;letter-spacing:.3px;line-height:1.3;order:-1}
  .card .cbadge.generic{background:rgba(255,255,255,.12);color:#cdd9e6}
  .card .cbadge.ops{background:rgba(255,153,0,.25);color:var(--orange2)}
  .card .cbadge.aws{background:rgba(51,204,255,.22);color:var(--cyan)}
  .card .cbadge.pxt{background:rgba(74,222,128,.22);color:var(--green)}
  .card .cbadge.lnd{background:rgba(165,107,255,.25);color:#c79bff}
  .card .cbadge.lastmile{background:rgba(51,204,255,.22);color:var(--cyan)}
  .card .cbadge.legal{background:rgba(255,80,80,.22);color:#ff8a8a}
  .card .cbadge.finance{background:rgba(255,210,77,.22);color:#ffd24d}
  .card .cbadge.security{background:rgba(74,222,128,.22);color:#8fffbf}
  .card .ctext{flex:1;font-size:12.5px;line-height:1.4;padding:8px 10px;color:#f2f7fc;display:flex;align-items:center;text-align:center;justify-content:center;text-shadow:0 1px 2px rgba(0,0,0,.6);word-spacing:1px}
  .card .ctext b{color:var(--orange2)} .card .ctext .kw{color:var(--cyan);font-weight:700}
  .card.selected{transform:translateY(-52px) scale(1.14)!important;box-shadow:0 0 0 3px var(--orange),0 18px 44px rgba(0,0,0,.7)!important;z-index:25}
  .combat-right{position:relative;z-index:4;display:flex;flex-direction:column;gap:8px;align-items:flex-end;justify-content:flex-end;padding-bottom:14px}
  .piles{display:flex;gap:16px;position:relative;z-index:4;padding:0 20px 10px;font-size:12px;color:var(--muted);justify-content:flex-end}
  .pile{display:flex;align-items:center;gap:5px;background:rgba(6,14,24,.6);padding:4px 10px;border-radius:16px;cursor:pointer}
  .pile b{color:var(--ink)}
  .targeting-hint{position:absolute;top:44%;left:50%;transform:transltranslate(-50%,-50%);z-index:15}

  /* ---------- overlays ---------- */
  .overlay-screen{position:absolute;inset:0;background:rgba(4,10,18,.86);z-index:40;display:none;align-items:center;justify-content:center;flex-direction:column;padding:24px;backdrop-filter:blur(3px)}
  .overlay-screen.active{display:flex}
  .panel{background:linear-gradient(180deg,var(--navy2),var(--navy));border:2px solid var(--panel2);border-radius:18px;padding:26px 30px;max-width:900px;width:100%;box-shadow:var(--shadow);max-height:90vh;overflow-y:auto}
  .panel h2{font-size:26px;margin-bottom:6px;text-align:center;color:var(--orange2)}
  .panel .psub{text-align:center;color:var(--muted);margin-bottom:20px;font-size:14px}
  .rewardgrid{display:flex;flex-wrap:wrap;gap:16px;justify-content:center;margin-bottom:20px}
  .cardrow{display:flex;gap:16px;justify-content:center;flex-wrap:wrap}
  .reward-item{background:var(--panel);border:2px solid var(--panel2);border-radius:12px;padding:12px 18px;cursor:pointer;display:flex;align-items:center;gap:12px;font-weight:700;transition:transform .1s,border-color .1s;min-width:220px}
  .reward-item:hover{transform:translateY(-3px);border-color:var(--orange)}
  .reward-item .ic{font-size:26px}
  .reward-item.taken{opacity:.4;pointer-events:none}
  .reward-item.cantafford{opacity:.55;border-color:#5a3a3a}
  .reward-item#rw-revive-,.reward-item[id^='rw-revive']{border-color:var(--gold)}
  .center-actions{display:flex;gap:14px;justify-content:center;margin-top:10px;flex-wrap:wrap}
  .pickcard{transform:scale(.92)}
  .pickcard .card{cursor:pointer}
  .pickcard:hover .card{border-color:var(--orange);box-shadow:0 0 22px rgba(255,153,0,.5)}
  .shopitem{position:relative}
  .price{position:absolute;bottom:-10px;left:50%;transform:translateX(-50%);background:var(--gold);color:#241300;font-weight:800;font-size:12px;padding:2px 10px;border-radius:12px;box-shadow:0 2px 6px #000;z-index:5;white-space:nowrap}
  .price.cant{background:#7a6a2a;color:#c9bd8a}
  .toast{position:fixed;top:70px;left:50%;transform:translateX(-50%);background:#050c16;border:1px solid var(--orange);color:var(--ink);padding:10px 20px;border-radius:10px;z-index:200;font-weight:700;box-shadow:var(--shadow);opacity:0;transition:opacity .25s,top .25s;pointer-events:none;max-width:80vw;text-align:center}
  .toast.show{opacity:1;top:80px}
  .end-screen{align-items:center;justify-content:center;text-align:center}
  .end-screen h1{font-size:56px;margin-bottom:10px}
  .win h1{color:var(--gold);text-shadow:0 0 30px rgba(255,210,77,.6)}
  .lose h1{color:var(--red)}
  .statgrid{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;max-width:560px;margin:16px auto 4px}
  .statcell{background:rgba(11,25,41,.72);border:1px solid var(--panel2);border-radius:12px;padding:10px 8px;text-align:center}
  .statcell .si{font-size:20px}
  .statcell .sv{font-size:20px;font-weight:900;color:var(--orange2);line-height:1.1;margin-top:2px}
  .statcell .sl{font-size:10px;color:var(--muted);text-transform:uppercase;letter-spacing:.4px;margin-top:2px}
  @media(max-width:560px){.statgrid{grid-template-columns:repeat(3,1fr)}}
  .helpgrid{display:grid;grid-template-columns:repeat(3,1fr);gap:20px;text-align:left}
  .helpcol h3{font-size:15px;color:var(--orange2);margin:12px 0 6px}
  .helpcol h3:first-child{margin-top:0}
  .helpcol p{font-size:12.5px;color:#cfe0f2;line-height:1.5;margin-bottom:4px}
  .helprow{display:flex;gap:8px;margin-bottom:6px;font-size:12px;line-height:1.4}
  .helprow .hk{flex:none;width:96px;font-weight:700;color:var(--cyan)}
  .helprow .hv{flex:1;color:#cfe0f2}
  @media(max-width:820px){.helpgrid{grid-template-columns:1fr}}
  .evt-art{width:130px;height:130px;border-radius:14px;object-fit:cover;margin:0 auto 14px;display:block;border:2px solid var(--panel2)}
  .log{position:absolute;top:64px;left:50%;transform:translateX(-50%);z-index:6;width:auto;max-width:60%;max-height:96px;overflow:hidden;display:flex;flex-direction:column-reverse;align-items:center;gap:3px;pointer-events:none;font-size:11px}
  .log div{background:rgba(5,12,22,.72);padding:3px 12px;border-radius:12px;color:var(--ink);animation:fadein .3s;text-align:center;border:1px solid rgba(255,255,255,.08);backdrop-filter:blur(2px)}
  @keyframes fadein{from{opacity:0}to{opacity:1}}
  .kwrow{font-size:11px;color:var(--muted);text-align:center;margin-top:10px;line-height:1.6}
  .kwrow b{color:var(--cyan)}
  .miniheader{display:flex;justify-content:space-between;align-items:center;margin-bottom:14px}
  .miniheader .g{color:var(--gold);font-weight:800}
  ::-webkit-scrollbar{width:10px;height:10px}
  ::-webkit-scrollbar-track{background:var(--navy)}
  ::-webkit-scrollbar-thumb{background:var(--panel2);border-radius:6px}
  #fxLayer{position:fixed;top:0;left:0;width:100vw;height:100vh;overflow:hidden;pointer-events:none;z-index:75}
  .hidden{display:none!important}
</style>
</head>
<body>
<div id="app">

  <!-- TITLE -->
  <div id="title" class="screen active">
    <div class="overlay"></div>
    <div class="content">
      <div class="tag">An Amazon Roguelike Deckbuilder</div>
      <h1>PEAK <span class="a">SEASON</span></h1>
      <div class="sub">Fight your way up the corporate tower. Battle Scope Creep, survive The Reorg, and defeat the dreaded <b>QBR</b>. Build your deck, collect mechanisms, and Deliver Results.™</div>
      <div id="continueWrap" style="margin:6px 0 2px"></div>
      <div class="tag" style="margin-top:6px" id="chooseTag">Assemble your party of 2</div>
      <div class="classpick" id="classpick"></div>
      <div id="partyStart" style="margin-top:16px;min-height:52px;display:flex;align-items:center;justify-content:center"></div>
      <div class="kwrow" style="max-width:640px;margin-top:18px">
        <b>How to play:</b> Play cards from your hand by spending ⚡ energy. Attacks deal damage, Skills grant Block (temporary shield), Powers give permanent buffs. End your turn to let enemies act. Clear the map to reach the boss.
      </div>
    </div>
  </div>

  <!-- MAP -->
  <div id="map" class="screen">
    <div class="topbar">
      <div class="who"><img id="heroFace" src=""><span id="heroName"></span></div>
      <div class="pill hp">❤️ <span id="hpText"></span><span class="hpbar"><i id="hpFill"></i></span></div>
      <div class="pill gold">🪙 <span id="goldText">0</span></div>
      <div class="pill floor">🏢 <span id="floorText"></span></div>
      <div class="spacer"></div>
      <div class="potionbelt" id="potionBelt"></div>
      <div class="relictray" id="relicTray"></div>
      <button class="btn ghost" id="deckBtn" style="padding:8px 14px;font-size:13px">🃏 Deck</button>
      <button class="btn ghost" id="helpBtn" style="padding:8px 12px;font-size:13px">❓ Help</button>
    </div>
    <div id="mapLegend"></div>
    <div id="mapScroll"><div id="mapCanvas"></div></div>
  </div>

  <!-- COMBAT -->
  <div id="combat" class="screen">
    <div class="cbveil"></div>
    <div class="topbar cbtop">
      <div class="who"><img id="cbFace" src=""><span id="cbName"></span></div>
      <div class="pill hp">❤️ <span id="cbHpText"></span><span class="hpbar"><i id="cbHpFill"></i></span></div>
      <div class="pill" style="color:var(--cyan)">🛡️ <span id="cbBlock">0</span></div>
      <div class="pill gold">🪙 <span id="cbGold">0</span></div>
      <div class="spacer"></div>
      <div class="potionbelt" id="cbPotionBelt"></div>
      <div class="relictray" id="cbRelicTray"></div>
      <div class="pill floor">Turn <span id="turnText">1</span></div>
      <button class="btn ghost" id="sfxToggle" title="Toggle sound">🔊</button>
      <button class="btn ghost" id="musicToggle" title="Toggle music">🎵</button>
      <button class="btn ghost" id="helpBtnCb" title="Help">❓</button>
    </div>
    <div class="arena">
      <div class="side heroes" id="heroSide"></div>
      <div class="side enemies" id="enemySide"></div>
    </div>
    <div class="piles">
      <div class="pile" id="drawPile">🂠 Draw <b>0</b></div>
      <div class="pile" id="discardPile">🗑️ Discard <b>0</b></div>
      <div class="pile" id="exhaustPile">🔥 Exhaust <b>0</b></div>
    </div>
    <div class="handdock">
      <div class="energyorb"><b id="energyText">3</b><small>ENERGY</small></div>
      <div class="hand" id="hand"></div>
      <div class="combat-right">
        <button class="btn" id="endTurnBtn">End Turn ⏭️</button>
      </div>
    </div>
  </div>

  <!-- OVERLAYS -->
  <div id="rewardOverlay" class="overlay-screen"><div class="panel" id="rewardPanel"></div></div>
  <div id="cardpickOverlay" class="overlay-screen"><div class="panel" id="cardpickPanel"></div></div>
  <div id="restOverlay" class="overlay-screen"><div class="panel" id="restPanel"></div></div>
  <div id="eventOverlay" class="overlay-screen"><div class="panel" id="eventPanel"></div></div>
  <div id="shopOverlay" class="overlay-screen"><div class="panel" id="shopPanel"></div></div>
  <div id="deckOverlay" class="overlay-screen"><div class="panel" id="deckPanel"></div></div>
  <div id="helpOverlay" class="overlay-screen"><div class="panel" id="helpPanel"></div></div>
  <div id="endOverlay" class="overlay-screen end-screen"><div class="content" id="endContent"></div></div>
  <div id="toast" class="toast"></div>
  <div id="dmgVeil"></div>
  <div id="fxLayer"><div id="comboPop"><div class="cnum"></div><div class="cmsg"></div></div></div>
</div>

<script>
"use strict";
/* ============================================================
   PEAK SEASON — a self-contained roguelike deckbuilder
   ============================================================ */
const A = "assets/";
const $ = s => document.querySelector(s);
const rnd = n => Math.floor(Math.random()*n);
const pick = arr => arr[rnd(arr.length)];
const clamp = (v,a,b)=>Math.max(a,Math.min(b,v));
function shuffle(a){for(let i=a.length-1;i>0;i--){const j=rnd(i+1);[a[i],a[j]]=[a[j],a[i]];}return a;}
let uid=1; const newId=()=>uid++;

/* ---------- KEYWORDS (tooltips) ---------- */
const KW = {
  Block:"Reduces incoming damage this turn. Lost at start of your turn (unless stated).",
  Vulnerable:"Takes 50% more attack damage. Counts down each turn.",
  Weak:"Deals 25% less attack damage. Counts down each turn.",
  Strength:"Increases attack damage by its value per hit.",
  Dexterity:"Increases Block gained from cards by its value.",
  Frail:"Gains 25% less Block from cards. Counts down each turn.",
  Ramp:"AWS mechanic: gain +1 Strength whenever you play 3 cards in a turn.",
  Metrics:"Deal bonus damage equal to cards played this turn.",
  Exhaust:"When played, the card is removed for the rest of combat.",
  Lifesteal:"Heal HP equal to a portion of the damage this attack deals.",
  Overwork:"Costs HP to play — spend health for a powerful effect.",
  Taunt:"Forces enemies to attack this character on their next turn.",
  Pierce:"Ignores the enemy Block - damage hits their HP directly.",
  Injunction:"Enjoined enemies skip their next turn.",
  Exploit:"Poison — deals its value in damage at the start of the enemy turn, then decays by 1.",
  Thorns:"Retaliate: deal this much damage back whenever you are attacked.",
  Escalate:"Each copy played this combat gets stronger.",
  Regen:"Heal this much at the start of your turn; decreases by 1 each turn."
};
function kwText(t){
  // wrap known keywords in the card text
  return t.replace(/\b(Block|Vulnerable|Weak|Strength|Dexterity|Frail|Exhaust|Ramp|Metrics|Regen)\b/g,
    '<span class="kw" title="$1">$1</span>');
}

/* ============================================================
   CARD DEFINITIONS
   type: attack | skill | power
   ============================================================ */
const CARDS = {
  /* ---- Basic / shared ---- */
  strike:{name:"Deep Dive",type:"attack",cost:1,rarity:"basic",art:"🔍",
    text:"Deal <b>{d}</b> damage.", d:6, du:9,
    play:(g,c,t)=>dealDamage(g,t,val(c,'d'))},
  defend:{name:"Backlog Grooming",type:"skill",cost:1,rarity:"basic",art:"📋",
    text:"Gain <b>{b}</b> Block.", b:5, bu:8,
    play:(g,c)=>gainBlock(g,g.hero,val(c,'b'))},

  /* ---- Operations (block / attrition) ---- */
  andon:{name:"Pull the Andon Cord",type:"skill",cost:1,rarity:"common",cls:"ops",art:"🚨",
    text:"Gain <b>{b}</b> Block. Apply 1 Weak to a random enemy.", b:6, bu:9,
    play:(g,c)=>{gainBlock(g,g.hero,val(c,'b'));const e=randLiveEnemy(g);if(e)applyStatus(g,e,'Weak',1);}},
  fivewhys:{name:"Five Whys",type:"attack",cost:1,rarity:"common",cls:"ops",art:"❓",
    text:"Deal <b>{d}</b> damage. If enemy is Vulnerable, draw 1 card.", d:7, du:10,
    play:(g,c,t)=>{dealDamage(g,t,val(c,'d'));if(t.status.Vulnerable>0)drawCards(g,1);}},
  standup:{name:"Daily Standup",type:"skill",cost:1,rarity:"common",cls:"ops",art:"🧍",
    text:"Gain <b>{b}</b> Block. Draw 1 card.", b:5, bu:7,
    play:(g,c)=>{gainBlock(g,g.hero,val(c,'b'));drawCards(g,1);}},
  safety:{name:"Safety First",type:"power",cost:1,rarity:"common",cls:"ops",art:"🦺",
    text:"Gain <b>{s}</b> Dexterity.", s:2, su:3,
    play:(g,c)=>applyStatus(g,g.hero,'Dexterity',val(c,'s'))},
  process:{name:"Process Improvement",type:"attack",cost:2,rarity:"uncommon",cls:"ops",art:"⚙️",
    text:"Deal <b>{d}</b> damage. Apply <b>{v}</b> Vulnerable.", d:10, du:14, v:2, vu:3,
    play:(g,c,t)=>{dealDamage(g,t,val(c,'d'));applyStatus(g,t,'Vulnerable',val(c,'v'));}},
  attrition:{name:"War of Attrition",type:"skill",cost:1,rarity:"uncommon",cls:"ops",art:"🛡️",
    text:"Gain Block equal to <b>{b}</b> + your current Block ÷ 2.", b:4, bu:7,
    play:(g,c)=>gainBlock(g,g.hero,val(c,'b')+Math.floor(g.hero.block/2))},
  metricsdash:{name:"Metrics Dashboard",type:"attack",cost:1,rarity:"uncommon",cls:"ops",art:"📊",
    text:"Deal damage equal to <b>{d}</b> + cards played this turn.", d:5, du:8,
    play:(g,c,t)=>dealDamage(g,t,val(c,'d')+g.cardsPlayedThisTurn)},
  slam:{name:"Ship It!",type:"attack",cost:2,rarity:"rare",cls:"ops",art:"📦",
    text:"Deal <b>{d}</b> damage. Gain <b>{b}</b> Block.", d:14, du:18, b:8, bu:12,
    play:(g,c,t)=>{dealDamage(g,t,val(c,'d'));gainBlock(g,g.hero,val(c,'b'));}},
  raisethebar:{name:"Raise the Bar",type:"power",cost:2,rarity:"rare",cls:"ops",art:"⬆️",
    text:"Gain <b>{s}</b> Strength and <b>{s2}</b> Dexterity.", s:2, su:3, s2:2, s2u:3,
    play:(g,c)=>{applyStatus(g,g.hero,'Strength',val(c,'s'));applyStatus(g,g.hero,'Dexterity',val(c,'s2'));}},
  osha:{name:"OSHA Compliance",type:"power",cls:"ops",cost:1,rarity:"uncommon",art:"🦺",
    text:"At end of turn, heal HP equal to <b>{h}%</b> of your Block.", h:20, hu:30,
    play:(g,c)=>{g.hero.powers.blockHealPct=(g.hero.powers.blockHealPct||0)+val(c,'h');}},
  peakprep:{name:"Peak Readiness",type:"skill",cls:"ops",cost:3,rarity:"rare",art:"🏭",
    text:"Gain <b>{b}</b> Block. Apply 2 Weak to ALL enemies. Draw 2.", b:22, bu:30,
    play:(g,c)=>{gainBlock(g,g.hero,val(c,'b'));liveEnemies(g).forEach(e=>applyStatus(g,e,'Weak',2));drawCards(g,2);}},

  /* ---- AWS (scaling / combo / ramp) ---- */
  scale:{name:"Auto Scaling",type:"power",cost:1,rarity:"common",cls:"aws",art:"📈",
    text:"Gain <b>{s}</b> Strength.", s:2, su:3,
    play:(g,c)=>applyStatus(g,g.hero,'Strength',val(c,'s'))},
  lambda:{name:"Lambda Burst",type:"attack",cost:0,rarity:"common",cls:"aws",art:"⚡",
    text:"Deal <b>{d}</b> damage. <span class='kw'>Exhaust</span>.", d:4, du:7, exhaust:true,
    play:(g,c,t)=>dealDamage(g,t,val(c,'d'))},
  query:{name:"Query Optimizer",type:"skill",cost:1,rarity:"common",cls:"aws",art:"🔧",
    text:"Draw <b>{n}</b> cards. Gain <b>{b}</b> Block.", n:2, nu:3, b:3, bu:5,
    play:(g,c)=>{drawCards(g,val(c,'n'));gainBlock(g,g.hero,val(c,'b'));}},
  parallel:{name:"Parallelize",type:"attack",cost:1,rarity:"common",cls:"aws",art:"⛓️",
    text:"Deal <b>{d}</b> damage to ALL enemies.", d:5, du:8, aoe:true,
    play:(g,c)=>liveEnemies(g).forEach(e=>dealDamage(g,e,val(c,'d')))},
  cache:{name:"Edge Cache",type:"skill",cost:0,rarity:"uncommon",cls:"aws",art:"💾",
    text:"Gain <b>{b}</b> Block. Draw 1 card. <span class='kw'>Exhaust</span>.", b:4, bu:6, exhaust:true,
    play:(g,c)=>{gainBlock(g,g.hero,val(c,'b'));drawCards(g,1);}},
  pipeline:{name:"CI/CD Pipeline",type:"attack",cost:2,rarity:"uncommon",cls:"aws",art:"🔁",
    text:"Deal <b>{d}</b> damage. Deal it again for each Power you've played.", d:8, du:11,
    play:(g,c,t)=>{const hits=1+g.powersPlayed;for(let i=0;i<hits;i++)dealDamage(g,t,val(c,'d'));}},
  redshift:{name:"Redshift Query",type:"attack",cost:1,rarity:"uncommon",cls:"aws",art:"🟥",
    text:"Deal <b>{d}</b> damage + <b>{s}</b> per Strength you have.", d:6, du:9, s:2, su:3,
    play:(g,c,t)=>dealDamage(g,t,val(c,'d')+g.hero.status.Strength*val(c,'s'))},
  fleet:{name:"EC2 Fleet",type:"attack",cost:2,rarity:"rare",cls:"aws",art:"🖥️",
    text:"Deal <b>{d}</b> damage {n} times.", d:4, du:6, n:3, nu:4,
    play:(g,c,t)=>{for(let i=0;i<val(c,'n');i++)dealDamage(g,t,val(c,'d'));}},
  reinvent:{name:"Re:Invent",type:"power",cost:2,rarity:"rare",cls:"aws",art:"🚀",
    text:"At the start of each turn, gain <b>{s}</b> Strength.", s:1, su:2,
    play:(g,c)=>{g.hero.powers.reinvent=(g.hero.powers.reinvent||0)+val(c,'s');}},
  drain:{name:"Data Siphon",type:"attack",cls:"aws",cost:2,rarity:"uncommon",art:"🩸",lifesteal:1.0,
    text:"Deal <b>{d}</b> damage. <span class='kw'>Lifesteal</span> 100% (heal for the full damage dealt).", d:8, du:12,
    play:(g,c,t)=>dealDamage(g,t,val(c,'d'))},
  megascale:{name:"Hyperscale",type:"attack",cls:"aws",cost:3,rarity:"rare",art:"🌐",
    text:"Deal <b>{d}</b> damage, then deal <b>{d}</b> again for each Power you've played.", d:12, du:16,
    play:(g,c,t)=>{const hits=1+g.powersPlayed;for(let i=0;i<hits;i++)dealDamage(g,t,val(c,'d'));}},

  /* ---- PXT (People Experience & Tech — heal / buff / support) ---- */
  wellness:{name:"Wellness Check",type:"skill",cost:1,rarity:"common",cls:"pxt",art:"💚",
    text:"Heal <b>{h}</b> HP. Gain <b>{b}</b> Block.", h:5, hu:8, b:3, bu:5,
    play:(g,c)=>{healHero(g,val(c,'h'));gainBlock(g,g.hero,val(c,'b'));}},
  recognition:{name:"Peer Recognition",type:"power",cost:1,rarity:"common",cls:"pxt",art:"🏅",
    text:"At the start of each turn, heal <b>{h}</b> HP.", h:2, hu:3,
    play:(g,c)=>{applyStatus(g,g.hero,'Regen',val(c,'h'));}},
  benefits:{name:"Open Enrollment",type:"skill",cost:2,rarity:"uncommon",cls:"pxt",art:"📋",
    text:"Heal <b>{h}</b> HP. Gain <b>{s}</b> Dexterity.", h:8, hu:12, s:2, su:3,
    play:(g,c)=>{healHero(g,val(c,'h'));applyStatus(g,g.hero,'Dexterity',val(c,'s'));}},
  mentor:{name:"Mentorship",type:"skill",cost:1,rarity:"uncommon",cls:"pxt",art:"🤝",
    text:"Gain <b>{b}</b> Block. Heal <b>{h}</b> if you're below half HP.", b:6, bu:9, h:6, hu:9,
    play:(g,c)=>{gainBlock(g,g.hero,val(c,'b'));if(g.hero.hp<g.hero.maxHp/2)healHero(g,val(c,'h'));}},
  townhall:{name:"Town Hall",type:"attack",cost:1,rarity:"uncommon",cls:"pxt",art:"📢",
    text:"Deal <b>{d}</b> damage to ALL enemies. Heal <b>{h}</b> HP.", d:5, du:8, h:3, hu:5, aoe:true,
    play:(g,c)=>{liveEnemies(g).forEach(e=>dealDamage(g,e,val(c,'d')));healHero(g,val(c,'h'));}},
  culture:{name:"Culture of Ownership",type:"power",cost:2,rarity:"rare",cls:"pxt",art:"🌟",
    text:"Whenever you gain Block, heal <b>{h}</b> HP.", h:2, hu:3,
    play:(g,c)=>{g.hero.powers.culture=(g.hero.powers.culture||0)+val(c,'h');}},
  sabbatical:{name:"Sabbatical",type:"skill",cost:2,rarity:"rare",cls:"pxt",art:"🏖️",
    text:"Heal <b>{h}</b> HP. Remove all Weak and Frail. Gain <b>{b}</b> Block.", h:14, hu:20, b:6, bu:10,
    play:(g,c)=>{healHero(g,val(c,'h'));g.hero.status.Weak=0;g.hero.status.Frail=0;gainBlock(g,g.hero,val(c,'b'));}},
  benefitsfair:{name:"Benefits Fair",type:"power",cls:"pxt",cost:2,rarity:"rare",art:"🎪",
    text:"Whenever you play a Skill, heal <b>{h}</b> HP.", h:2, hu:3,
    play:(g,c)=>{g.hero.powers.skillHeal=(g.hero.powers.skillHeal||0)+val(c,'h');}},
  offsite:{name:"Team Offsite",type:"skill",cls:"pxt",cost:3,rarity:"rare",art:"🏝️",
    text:"Heal <b>{h}</b> HP. Gain <b>{b}</b> Block. Remove ALL your debuffs. Gain 1 Strength & 1 Dexterity.", h:20, hu:28, b:12, bu:16,
    play:(g,c)=>{healHero(g,val(c,'h'));gainBlock(g,g.hero,val(c,'b'));g.hero.status.Weak=0;g.hero.status.Vulnerable=0;g.hero.status.Frail=0;applyStatus(g,g.hero,'Strength',1);applyStatus(g,g.hero,'Dexterity',1);}},

  /* ---- Learning & Development (draw / upgrade / knowledge engine) ---- */
  onboarding:{name:"Onboarding",type:"skill",cost:1,rarity:"common",cls:"lnd",art:"📚",
    text:"Draw <b>{n}</b> cards.", n:2, nu:3,
    play:(g,c)=>drawCards(g,val(c,'n'))},
  flashcards:{name:"Flashcards",type:"attack",cost:1,rarity:"common",cls:"lnd",art:"🗂️",
    text:"Deal <b>{d}</b> damage. Draw 1 card.", d:5, du:8,
    play:(g,c,t)=>{dealDamage(g,t,val(c,'d'));drawCards(g,1);}},
  certification:{name:"Certification",type:"power",cost:1,rarity:"common",cls:"lnd",art:"🎓",
    text:"Draw 1 additional card at the start of each turn.", extraDraw:1,
    play:(g,c)=>{g.hero.powers.extraDraw=(g.hero.powers.extraDraw||0)+1;}},
  workshop:{name:"Workshop",type:"skill",cost:1,rarity:"uncommon",cls:"lnd",art:"🛠️",
    text:"Draw <b>{n}</b> cards. Gain <b>{b}</b> Block.", n:2, nu:3, b:4, bu:6,
    play:(g,c)=>{drawCards(g,val(c,'n'));gainBlock(g,g.hero,val(c,'b'));}},
  knowledge:{name:"Knowledge Sharing",type:"attack",cost:1,rarity:"uncommon",cls:"lnd",art:"💡",
    text:"Deal damage equal to <b>{d}</b> + 2× cards in your hand.", d:6, du:9,
    play:(g,c,t)=>dealDamage(g,t,val(c,'d')+g.hand.length*2)},
  curriculum:{name:"Curriculum Design",type:"skill",cost:0,rarity:"uncommon",cls:"lnd",art:"📝",
    text:"Draw 1 card. Gain 1 energy. <span class='kw'>Exhaust</span>.", exhaust:true,
    play:(g,c)=>{drawCards(g,1);g.energy++;}},
  masterclass:{name:"Masterclass",type:"attack",cost:2,rarity:"rare",cls:"lnd",art:"🏆",
    text:"Deal <b>{d}</b> damage. Deal <b>{d2}</b> more for each card drawn this turn.", d:8, du:12, d2:3, d2u:4,
    play:(g,c,t)=>dealDamage(g,t,val(c,'d')+(g.cardsDrawnThisTurn||0)*val(c,'d2'))},
  synergy:{name:"Cross-Training",type:"power",cost:2,rarity:"rare",cls:"lnd",art:"🔀",
    text:"Whenever you draw a card, gain <b>{b}</b> Block.", b:1, bu:2,
    play:(g,c)=>{g.hero.powers.crossTrain=(g.hero.powers.crossTrain||0)+val(c,'b');}},
  retrospective:{name:"Retrospective",type:"skill",cls:"lnd",cost:1,rarity:"uncommon",art:"🔎",
    text:"Draw <b>{n}</b> cards. Heal 1 HP for each card in your hand afterward.", n:2, nu:3,
    play:(g,c)=>{drawCards(g,val(c,'n'));healHero(g,g.hand.length);}},

  /* ---- Last Mile (Swarm — summon tokens that act each turn) ---- */
  dispatch:{name:"Dispatch Driver",type:"skill",cls:"lastmile",cost:1,rarity:"common",art:"🚘",
    text:"Summon a <b>Driver</b> (deals 3 to a random enemy each turn).",
    play:(g,c)=>summonToken('driver',1)},
  packagedrop:{name:"Package Drop",type:"skill",cls:"lastmile",cost:1,rarity:"common",art:"📦",
    text:"Summon a <b>Package</b> (grants 3 Block each turn). Gain <b>{b}</b> Block now.", b:3, bu:5,
    play:(g,c)=>{summonToken('package',1);gainBlock(g,g.hero,val(c,'b'));}},
  routeopt:{name:"Route Optimization",type:"skill",cls:"lastmile",cost:1,rarity:"common",art:"🗺️",
    text:"Draw 1 card. Your Drivers deal <b>{d}</b> bonus damage this fight.", d:1, du:2,
    play:(g,c)=>{drawCards(g,1);g.tokens.forEach(t=>{if(t.kind==='driver')t.dmg+=val(c,'d');});}},
  fleetsurge:{name:"Fleet Surge",type:"skill",cls:"lastmile",cost:2,rarity:"uncommon",art:"🚚",
    text:"Summon <b>{n}</b> Drivers.", n:2, nu:3,
    play:(g,c)=>summonToken('driver',val(c,'n'))},
  sameday:{name:"Same-Day Delivery",type:"skill",cls:"lastmile",cost:0,rarity:"uncommon",art:"⚡",
    text:"Summon a Package. Draw 1 card. <span class='kw'>Exhaust</span>.", exhaust:true,
    play:(g,c)=>{summonToken('package',1);drawCards(g,1);}},
  overnight:{name:"Overnight Blitz",type:"attack",cls:"lastmile",cost:2,rarity:"rare",art:"🌙",
    text:"All your units act immediately. Then deal <b>{d}</b> damage.", d:6, du:9,
    play:(g,c,t)=>{ g.tokens.forEach(tk=>{ if(tk.kind==='driver'){const e=randLiveEnemy(g);if(e)dealDamage(g,e,tk.dmg+densityBonus());} else if(tk.kind==='package'){gainBlock(g,g.hero,tk.block+densityBonus());} }); if(t)dealDamage(g,t,val(c,'d')); }},
  logistics:{name:"Logistics Network",type:"power",cls:"lastmile",cost:2,rarity:"rare",art:"🔗",
    text:"At the start of each turn, summon a Driver.",
    play:(g,c)=>{g.hero.powers.autoDriver=(g.hero.powers.autoDriver||0)+1;}},

  /* ---- Legal & Trust (Control — debuff & lockdown) ---- */
  compliance:{name:"Compliance Review",type:"skill",cls:"legal",cost:1,rarity:"common",art:"📋",
    text:"Apply <b>{v}</b> Vulnerable to ALL enemies.", v:2, vu:3, aoe:true,
    play:(g,c)=>liveEnemies(g).forEach(e=>applyStatus(g,e,'Vulnerable',val(c,'v')))},
  noncompete:{name:"Non-Compete Clause",type:"skill",cls:"legal",cost:1,rarity:"common",art:"✍️",
    text:"Apply <b>{w}</b> Weak to an enemy. Draw 1 card.", w:2, wu:3,
    play:(g,c,t)=>{ if(t)applyStatus(g,t,'Weak',val(c,'w')); else liveEnemies(g).forEach(e=>applyStatus(g,e,'Weak',val(c,'w'))); drawCards(g,1);}},
  cease:{name:"Cease & Desist",type:"attack",cls:"legal",cost:1,rarity:"common",art:"🛑",
    text:"Deal <b>{d}</b> damage. Apply <b>{f}</b> Frail.", d:6, du:9, f:2, fu:3,
    play:(g,c,t)=>{dealDamage(g,t,val(c,'d'));applyStatus(g,t,'Frail',val(c,'f'));}},
  injunction:{name:"Injunction",type:"skill",cls:"legal",cost:2,rarity:"uncommon",art:"⚖️",
    text:"An enemy is <b>enjoined</b> — it skips its next turn.",
    play:(g,c,t)=>{ const e=t||randLiveEnemy(g); if(e){ e.stun=(e.stun||0)+1; log(g,`⚖️ ${e.name} will skip its next turn!`);} }},
  litigate:{name:"Litigate",type:"attack",cls:"legal",cost:1,rarity:"uncommon",art:"⚔️",
    text:"Deal <b>{d}</b> damage, +<b>{b}</b> for each debuff on the target.", d:6, du:8, b:4, bu:5,
    play:(g,c,t)=>{ let deb=0; if(t){['Vulnerable','Weak','Frail'].forEach(s=>{if(t.status[s]>0)deb++;});} dealDamage(g,t,val(c,'d')+deb*val(c,'b')); }},
  classaction:{name:"Class Action",type:"attack",cls:"legal",cost:2,rarity:"rare",art:"👨‍⚖️",
    text:"Deal <b>{d}</b> damage to ALL enemies. Apply <b>{w}</b> Weak & <b>{v}</b> Vulnerable to all.", d:10, du:14, w:2, wu:2, v:2, vu:2, aoe:true,
    play:(g,c)=>liveEnemies(g).forEach(e=>{dealDamage(g,e,val(c,'d'));applyStatus(g,e,'Weak',val(c,'w'));applyStatus(g,e,'Vulnerable',val(c,'v'));})},
  gagorder:{name:"Gag Order",type:"power",cls:"legal",cost:2,rarity:"rare",art:"🤐",
    text:"Whenever you apply a debuff, deal <b>{d}</b> damage to that enemy.", d:2, du:3,
    play:(g,c)=>{g.hero.powers.gagOrder=(g.hero.powers.gagOrder||0)+val(c,'d');}},

  /* ---- Finance (FP&A) — economy / gold as fuel ---- */
  invoice:{name:"Invoice",type:"attack",cls:"finance",cost:1,rarity:"common",art:"🧾",
    text:"Deal <b>{d}</b> damage. Gain <b>{g}</b> gold.", d:6, du:8, g:6, gu:9,
    play:(g,c,t)=>{dealDamage(g,t,val(c,'d'));g.gold+=val(c,'g');renderTop&&renderTop();}},
  costsavings:{name:"Cost Savings",type:"skill",cls:"finance",cost:1,rarity:"common",art:"💵",
    text:"Gain <b>{b}</b> Block and <b>{g}</b> gold.", b:5, bu:7, g:5, gu:8,
    play:(g,c)=>{gainBlock(g,g.hero,val(c,'b'));g.gold+=val(c,'g');renderTop&&renderTop();}},
  roi:{name:"Return on Investment",type:"attack",cls:"finance",cost:1,rarity:"uncommon",art:"📊",
    text:"Deal damage equal to <b>{d}</b> + 1 per 15 gold you have.", d:5, du:7,
    play:(g,c,t)=>dealDamage(g,t,val(c,'d')+Math.floor(g.gold/15))},
  reallocate:{name:"Reallocate Budget",type:"attack",cls:"finance",cost:1,rarity:"uncommon",art:"💸",
    text:"Spend <b>{s}</b> gold (if able): deal <b>{d}</b> damage. Otherwise deal 4.", s:10, su:10, d:16, du:22,
    play:(g,c,t)=>{ if(g.gold>=val(c,'s')){ g.gold-=val(c,'s'); dealDamage(g,t,val(c,'d')); renderTop&&renderTop(); } else { dealDamage(g,t,4); } }},
  hedge:{name:"Hedge Fund",type:"skill",cls:"finance",cost:2,rarity:"rare",art:"🏦",
    text:"Gain Block equal to <b>{b}</b> + 1 per 12 gold you have.", b:6, bu:9,
    play:(g,c)=>gainBlock(g,g.hero,val(c,'b')+Math.floor(g.gold/12))},
  buyback:{name:"Stock Buyback",type:"power",cls:"finance",cost:2,rarity:"rare",art:"📈",
    text:"At the start of each turn, gain <b>{g}</b> more gold.", g:4, gu:6,
    play:(g,c)=>{g.hero.powers.buyback=(g.hero.powers.buyback||0)+val(c,'g');}},

  /* ---- Security (InfoSec) — DoT / Exploit & Thorns ---- */
  injectvuln:{name:"Inject Vulnerability",type:"skill",cls:"security",cost:1,rarity:"common",art:"🐛",
    text:"Apply <b>{e}</b> <span class='kw'>Exploit</span> to an enemy.", e:4, eu:6,
    play:(g,c,t)=>{ const en=t||randLiveEnemy(g); if(en)applyStatus(g,en,'Exploit',val(c,'e')); }},
  patch:{name:"Patch",type:"skill",cls:"security",cost:1,rarity:"common",art:"🩹",
    text:"Gain <b>{b}</b> Block. Draw 1 card.", b:6, bu:8,
    play:(g,c)=>{gainBlock(g,g.hero,val(c,'b'));drawCards(g,1);}},
  firewall:{name:"Firewall",type:"skill",cls:"security",cost:1,rarity:"uncommon",art:"🧱",
    text:"Gain <b>{b}</b> Block and <b>{th}</b> <span class='kw'>Thorns</span> this turn.", b:6, bu:9, th:4, thu:6,
    play:(g,c)=>{gainBlock(g,g.hero,val(c,'b'));g.hero.thorns=(g.hero.thorns||0)+val(c,'th');}},
  scan:{name:"Vulnerability Scan",type:"attack",cls:"security",cost:1,rarity:"uncommon",art:"🔦",
    text:"Deal <b>{d}</b> damage. Apply <b>{e}</b> Exploit to ALL enemies.", d:5, du:7, e:2, eu:3, aoe:true,
    play:(g,c)=>liveEnemies(g).forEach(e=>{dealDamage(g,e,val(c,'d'));applyStatus(g,e,'Exploit',val(c,'e'));})},
  zeroday:{name:"Zero-Day Exploit",type:"attack",cls:"security",cost:2,rarity:"rare",art:"💥",
    text:"Deal <b>{d}</b> damage. Add <span class='kw'>Exploit</span> equal to the target's current Exploit (max +8).", d:8, du:12,
    play:(g,c,t)=>{ if(t){ dealDamage(g,t,val(c,'d')); if(t.alive&&t.status.Exploit>0){const add=Math.min(t.status.Exploit,8); t.status.Exploit+=add; floatText(t,`+${add} Exploit`,'dmg');} } }},
  honeypot:{name:"Honeypot",type:"power",cls:"security",cost:2,rarity:"rare",art:"🍯",
    text:"Gain <b>{th}</b> permanent <span class='kw'>Thorns</span> each turn.", th:3, thu:5,
    play:(g,c)=>{g.hero.powers.honeypot=(g.hero.powers.honeypot||0)+val(c,'th');}},
  keynote:{name:"Keynote Address",type:"attack",cls:"lnd",cost:3,rarity:"rare",art:"🎤",
    text:"Draw <b>{n}</b> cards, then deal damage to ALL enemies equal to <b>{d}</b> + cards in hand.", n:3, nu:4, d:6, du:9, aoe:true,
    play:(g,c)=>{drawCards(g,val(c,'n'));const dmg=val(c,'d')+g.hand.length;liveEnemies(g).forEach(e=>dealDamage(g,e,dmg));}},

  /* ---- Colorless / neutral (shop & rewards) ---- */
  escalate:{name:"Escalate to Leadership",type:"attack",cost:1,rarity:"uncommon",art:"📣",
    text:"Deal <b>{d}</b> damage. Costs 0 if it's the only card in hand.", d:9, du:13,
    play:(g,c,t)=>dealDamage(g,t,val(c,'d'))},
  twopizza:{name:"Two-Pizza Team",type:"skill",cost:1,rarity:"uncommon",art:"🍕",
    text:"Draw <b>{n}</b> cards.", n:3, nu:4,
    play:(g,c)=>drawCards(g,val(c,'n'))},
  pip:{name:"Issue a PIP",type:"attack",cost:1,rarity:"uncommon",art:"📉",
    text:"Deal <b>{d}</b> damage. Apply <b>{w}</b> Weak.", d:6, du:8, w:2, wu:3,
    play:(g,c,t)=>{dealDamage(g,t,val(c,'d'));applyStatus(g,t,'Weak',val(c,'w'));}},
  coffee:{name:"Cold Brew",type:"skill",cost:0,rarity:"common",art:"☕",
    text:"Gain 1 energy. Draw 1 card. <span class='kw'>Exhaust</span>.", exhaust:true,
    play:(g,c)=>{g.energy++;drawCards(g,1);}},
  snack:{name:"Micro-Kitchen Snack",type:"skill",cost:1,rarity:"common",art:"🍎",
    text:"Heal <b>{h}</b> HP. Draw 1 card.", h:6, hu:9,
    play:(g,c)=>{healHero(g,val(c,'h'));drawCards(g,1);}},
  taunt:{name:"Rally the Team",type:"skill",cost:0,rarity:"common",art:"📣",
    text:"<span class='kw'>Taunt</span>: enemies target this ally next turn. Gain <b>{b}</b> Block.", b:4, bu:7,
    play:(g,c)=>{ g.hero.taunt=1; gainBlock(g,g.hero,val(c,'b')); }},
  taunt_ops:{name:"I've Got This",type:"skill",cls:"ops",cost:0,rarity:"common",art:"🛡️",
    text:"<span class='kw'>Taunt</span>: enemies target this ally next turn. Gain <b>{b}</b> Block.", b:6, bu:9,
    play:(g,c)=>{ g.hero.taunt=1; gainBlock(g,g.hero,val(c,'b')); }},
  overridecard:{name:"Override Protocol",type:"attack",cost:1,rarity:"uncommon",art:"🗡️",pierce:true,
    text:"Deal <b>{d}</b> damage. <span class='kw'>Pierce</span> (ignores Block).", d:7, du:10,
    play:(g,c,t)=>dealDamage(g,t,val(c,'d'))},
  auditattack:{name:"Compliance Audit",type:"attack",cls:"lnd",cost:2,rarity:"rare",art:"🔍",pierce:true,
    text:"Deal <b>{d}</b> damage to ALL enemies. <span class='kw'>Pierce</span> (ignores Block).", d:8, du:12, aoe:true,
    play:(g,c)=>liveEnemies(g).forEach(e=>dealDamage(g,e,val(c,'d')))},
  sweep:{name:"Sweep the Floor",type:"attack",cls:"ops",cost:2,rarity:"uncommon",art:"🧹",
    text:"Deal <b>{d}</b> damage to ALL enemies. Gain <b>{b}</b> Block.", d:7, du:10, b:5, bu:8, aoe:true,
    play:(g,c)=>{liveEnemies(g).forEach(e=>dealDamage(g,e,val(c,'d')));gainBlock(g,g.hero,val(c,'b'));}},
  vampire:{name:"Feedback Loop",type:"attack",cost:1,rarity:"uncommon",art:"🩸",lifesteal:0.5,
    text:"Deal <b>{d}</b> damage. <span class='kw'>Lifesteal</span> 50% (heal half the damage dealt).", d:8, du:11,
    play:(g,c,t)=>dealDamage(g,t,val(c,'d'))},
  allnighter:{name:"Pull an All-Nighter",type:"attack",cost:1,rarity:"uncommon",art:"🌙",hpCost:5,
    text:"<span class='kw'>Overwork</span>: lose 5 HP. Deal <b>{d}</b> damage to ALL enemies.", d:11, du:15, aoe:true,
    play:(g,c)=>liveEnemies(g).forEach(e=>dealDamage(g,e,val(c,'d')))},
  secondwind:{name:"Second Wind",type:"power",cost:1,rarity:"uncommon",art:"🌬️",
    text:"At end of turn, heal <b>{h}</b> HP for each unspent energy.", h:2, hu:3,
    play:(g,c)=>{g.hero.powers.secondWind=(g.hero.powers.secondWind||0)+val(c,'h');}},
  worklife:{name:"Work-Life Balance",type:"skill",cost:2,rarity:"rare",art:"⚖️",
    text:"Heal to <b>{h}%</b> of your max HP if below it, then draw 2.", h:60, hu:75,
    play:(g,c)=>{const tgt=Math.floor(g.hero.maxHp*val(c,'h')/100);if(g.hero.hp<tgt)healHero(g,tgt-g.hero.hp);drawCards(g,2);}},
  bigbet:{name:"Big Bet",type:"attack",cost:3,rarity:"rare",art:"🎲",
    text:"Deal <b>{d}</b> damage. Apply <b>{v}</b> Vulnerable.", d:30, du:40, v:3, vu:4,
    play:(g,c,t)=>{dealDamage(g,t,val(c,'d'));applyStatus(g,t,'Vulnerable',val(c,'v'));}},
  reorg2:{name:"Full Reorg",type:"skill",cost:3,rarity:"rare",art:"🔀",
    text:"Exhaust your hand, then draw that many cards +2. Gain <b>{b}</b> Block.", b:10, bu:15, exhaust:true,
    play:(g,c)=>{const n=g.hand.length;g.exhaust.push(...g.hand);g.hand=[];drawCards(g,n+2);gainBlock(g,g.hero,val(c,'b'));}},
  pto:{name:"Take PTO",type:"skill",cost:1,rarity:"uncommon",art:"🌴",
    text:"Heal <b>{h}</b> HP. Gain <b>{b}</b> Block. <span class='kw'>Exhaust</span>.", h:10, hu:15, b:5, bu:8, exhaust:true,
    play:(g,c)=>{healHero(g,val(c,'h'));gainBlock(g,g.hero,val(c,'b'));}},
  workback:{name:"Working Backwards",type:"skill",cost:1,rarity:"rare",art:"📝",
    text:"Draw <b>{n}</b> cards. Gain 1 energy next turn.", n:2, nu:3,
    play:(g,c)=>{drawCards(g,val(c,'n'));g.bonusEnergyNext=(g.bonusEnergyNext||0)+1;}},
};

/* status floor damage bonus, val() reads base or upgraded */
function val(card, key){
  const def = CARDS[card.k];
  if(card.upgraded && def[key+'u']!==undefined) return def[key+'u'];
  return def[key];
}
function makeCard(k, upgraded=false){
  return {id:newId(), k, upgraded};
}

/* ============================================================
   CLASSES
   ============================================================ */
const CLASSES = {
  ops:{
    name:"Operations", role:"Tank · Block & Attrition", roleClass:"ops",
    img:"hero_operations.png", maxHp:80, energy:3,
    desc:"Outlast everything. Stack Block, wear enemies down, and never break under Peak.",
    starter:["strike","strike","strike","defend","defend","defend","andon","standup","attrition","safety"],
    startRelic:"andon_relic", innate:"🧱 Fortress: gain 2 Block at the start of each turn."
  },
  aws:{
    name:"AWS", role:"Scaling · Combo & Ramp", roleClass:"aws",
    img:"hero_aws.png", maxHp:68, energy:3,
    desc:"Start small, scale infinitely. Chain cards, stack Strength, and burst for massive damage.",
    starter:["strike","strike","strike","defend","defend","scale","lambda","lambda","query","redshift"],
    startRelic:"scale_relic", innate:"📈 Ramp: every 3rd card played each turn grants +1 Strength."
  },
  pxt:{
    name:"PXT", role:"Support · Heal & Sustain", roleClass:"pxt",
    img:"hero_pxt.png", maxHp:75, energy:3,
    desc:"Nobody burns out on your watch. Heal through anything, buff steadily, and win the long game.",
    starter:["strike","strike","strike","defend","defend","wellness","recognition","mentor","benefits"],
    startRelic:"wellness_relic", innate:"💚 Duty of Care: heal 1 HP at the start of each turn."
  },
  lnd:{
    name:"Learning & Dev", role:"Engine · Draw & Upgrade", roleClass:"lnd",
    img:"hero_lnd.png", maxHp:70, energy:3,
    desc:"Knowledge compounds. Draw deep, chain cards, and turn a full hand into overwhelming force.",
    starter:["strike","strike","strike","defend","defend","onboarding","flashcards","certification","knowledge"],
    startRelic:"cert_relic", innate:"📚 Engine: draw +1 card/turn. First attack each turn deals +1 damage per 2 cards drawn."
  },
  lastmile:{
    name:"Last Mile", role:"Swarm · Tokens & Tempo", roleClass:"lastmile",
    img:"hero_lastmile.png", maxHp:72, energy:3,
    desc:"Deploy a fleet. Summon Driver & Package units that chip away every turn — overwhelm with volume, not big hits.",
    starter:["strike","strike","strike","strike","defend","defend","defend","dispatch","dispatch","routeopt"],
    startRelic:"fleet_relic", innate:"🚚 Density: your summoned units deal +1 damage each."
  },
  legal:{
    name:"Legal & Trust", role:"Control · Debuff & Lockdown", roleClass:"legal",
    img:"hero_legal.png", maxHp:70, energy:3,
    desc:"Tie enemies up in red tape. Stack debuffs, skip their turns, and win without ever taking the hit.",
    starter:["strike","strike","strike","defend","defend","cease","injunction","compliance","noncompete"],
    startRelic:"legal_relic", innate:"⚖️ Precedent: the first debuff you apply each turn is +1 stronger."
  },
  finance:{
    name:"Finance (FP&A)", role:"Economy · Gold as Fuel", roleClass:"finance",
    img:"hero_finance.png", maxHp:70, energy:3,
    desc:"Turn money into power. Spend and earn gold mid-combat; your damage and block scale with the gold on hand.",
    starter:["strike","strike","strike","defend","defend","reallocate","costsavings","roi","invoice"],
    startRelic:"finance_relic", innate:"💰 Compounding: gain 3 gold at the start of each combat turn."
  },
  security:{
    name:"Security (InfoSec)", role:"DoT · Exploit & Thorns", roleClass:"security",
    img:"hero_security.png", maxHp:72, energy:3,
    desc:"Patch, then punish. Stack Exploit that ticks every turn, and retaliate with Thorns when attacked.",
    starter:["strike","strike","strike","defend","defend","injectvuln","firewall","patch","zeroday"],
    startRelic:"security_relic", innate:"🛡️ Threat Model: enemies with Exploit take +25% from your attacks."
  }
};

/* card pools per class for rewards/shops */
function classPool(cls){
  return Object.keys(CARDS).filter(k=>{
    const c=CARDS[k];
    if(c.rarity==='basic')return false;
    return !c.cls || c.cls===cls; // neutral + own class
  });
}

/* ============================================================
   POTIONS (consumables — use anytime in combat)
   ============================================================ */
const POTIONS = {
  heal:{name:"Cold Brew Potion",icon:"🧴",desc:"Heal 12 HP instantly.",
    use:(g)=>{healHero(g,12);}},
  block:{name:"Focus Tonic",icon:"🔵",desc:"Gain 12 Block.",
    use:(g)=>{gainBlock(g,g.hero,12);}},
  energy:{name:"Energy Drink",icon:"⚡",desc:"Gain 2 energy this turn.",
    combatOnly:true, use:(g)=>{g.energy+=2; renderCombat();}},
  strength:{name:"Pre-Workout",icon:"💪",desc:"Gain 2 Strength for this combat.",
    combatOnly:true, use:(g)=>{applyStatus(g,g.hero,'Strength',2);}},
  draw:{name:"Nootropic",icon:"🧠",desc:"Draw 3 cards.",
    combatOnly:true, use:(g)=>{drawCards(g,3);}},
  fire:{name:"Hot Sauce",icon:"🌶️",desc:"Deal 10 damage to ALL enemies.",
    combatOnly:true, use:(g)=>{liveEnemies(g).forEach(e=>dealDamage(g,e,10));}},
  cleanse:{name:"Spa Day",icon:"🧖",desc:"Heal 8 HP and remove all your debuffs.",
    use:(g)=>{healHero(g,8);g.hero.status.Weak=0;g.hero.status.Vulnerable=0;g.hero.status.Frail=0;renderCombat&&renderCombat();}},
};
const POTION_KEYS=Object.keys(POTIONS);
const MAX_POTIONS=3;
function addPotion(pk){
  if(!g.potions) g.potions=[];
  if(g.potions.length>=MAX_POTIONS){ toast("Potion belt full (3)"); return false; }
  g.potions.push(pk); toast(`Gained ${POTIONS[pk].name} ${POTIONS[pk].icon}`);
  renderPotions(); return true;
}
function usePotion(idx){
  if(!g.potions||!g.potions[idx])return;
  const pk=g.potions[idx]; const P=POTIONS[pk];
  const inCombat=$('#combat').classList.contains('active') && !g.combatOver;
  if(P.combatOnly && !inCombat){ toast("Can only use that in combat"); return; }
  g.potions.splice(idx,1);
  if(P.use) P.use(g);
  if(sfxOn) beep('sine',700,0.12,0.08,1100);
  renderPotions(); renderTop(); if(inCombat) renderCombat();
  checkCombatEnd && (inCombat && checkCombatEnd());
}
function renderPotions(){
  ['#potionBelt','#cbPotionBelt'].forEach(sel=>{
    const belt=document.querySelector(sel); if(!belt)return;
    belt.innerHTML='';
    const pots=g&&g.potions?g.potions:[];
    for(let i=0;i<MAX_POTIONS;i++){
      const slot=document.createElement('div'); slot.className='potionslot';
      if(pots[i]){
        const P=POTIONS[pots[i]];
        slot.classList.add('filled'); slot.textContent=P.icon;
        slot.innerHTML+=`<div class="tip"><b>${P.name}</b><br>${P.desc}<br><i style="color:var(--muted)">Click to use</i></div>`;
        slot.onclick=()=>usePotion(i);
      } else { slot.textContent='+'; slot.classList.add('empty'); }
      belt.appendChild(slot);
    }
  });
}
function rollPotion(){ return pick(POTION_KEYS); }

/* ============================================================
   RELICS (mechanisms)
   ============================================================ */
const RELICS = {
  andon_relic:{name:"Andon Cord",icon:"🚨",desc:"At the start of each combat, gain 4 Block.",
    onCombatStart:(g)=>gainBlock(g,g.hero,4)},
  scale_relic:{name:"Scaling Group",icon:"📈",desc:"At the start of each combat, gain 1 Strength.",
    onCombatStart:(g)=>applyStatus(g,g.hero,'Strength',1)},
  wellness_relic:{name:"Wellness Program",icon:"💚",desc:"At the start of each combat, heal 4 HP.",
    onCombatStart:(g)=>healHero(g,4)},
  cert_relic:{name:"Learning Library",icon:"🎓",desc:"On the first turn of each combat, draw 1 extra card.",
    onFirstTurn:(g)=>drawCards(g,1)},
  fleet_relic:{name:"Dispatch Board",icon:"🚚",desc:"At the start of each combat, summon a Driver unit.",
    onCombatStart:(g)=>{ if(g.tokens) summonToken('driver',1); }},
  legal_relic:{name:"Retainer Agreement",icon:"⚖️",desc:"At the start of each combat, apply 1 Weak to all enemies.",
    onCombatStart:(g)=>{ liveEnemies(g).forEach(e=>applyStatus(g,e,'Weak',1)); }},
  finance_relic:{name:"Signing Bonus",icon:"💰",desc:"Start each combat with +20 gold.",
    onCombatStart:(g)=>{ g.gold+=20; renderTop&&renderTop(); }},
  security_relic:{name:"IDS Sensor",icon:"🛡️",desc:"At the start of each combat, apply 3 Exploit to a random enemy.",
    onCombatStart:(g)=>{ const e=randLiveEnemy(g); if(e)applyStatus(g,e,'Exploit',3); }},
  coffee_relic:{name:"Espresso Machine",icon:"☕",desc:"At the start of each combat, gain 1 extra energy on turn 1.",
    onCombatStart:(g)=>{g.energy++;}},
  bar_raiser:{name:"Bar Raiser",icon:"🎯",desc:"Whenever you play 3 attacks in a turn, deal 4 damage to a random enemy.",
    onAttackPlayed:(g)=>{if(g.attacksThisTurn>0 && g.attacksThisTurn%3===0){const e=randLiveEnemy(g);if(e){dealDamage(g,e,4);log(g,"🎯 Bar Raiser strikes for 4!");}}}},
  wb_doc:{name:"6-Pager",icon:"📄",desc:"At the start of combat, draw 2 extra cards on turn 1.",
    onFirstTurn:(g)=>drawCards(g,2)},
   handbook:{name:"Owner's Manual",icon:"📕",desc:"Heal 6 HP whenever you enter a Rest site (in addition to resting).",
    passive:true},
  peccy:{name:"Peccy Pin",icon:"📌",desc:"Heal 2 HP at the end of each combat.",
    onCombatEnd:(g)=>healHero(g,2)},
  monitor:{name:"CloudWatch",icon:"📟",desc:"At the start of your turn, if you have no Block, gain 3 Block.",
    onTurnStart:(g)=>{if(g.hero.block===0)gainBlock(g,g.hero,3);}},
  cart:{name:"Golden Cart",icon:"🛒",desc:"Gain 25% more gold from combats.",passive:true},

  /* --- OPS-themed (block / defense) --- */
  forklift:{name:"Forklift Certification",icon:"🏗️",cls:"ops",desc:"The first time you gain Block each turn, gain 3 additional Block.",
    onTurnStart:(g)=>{g._forkliftReady=true;}},
  safetyvest:{name:"Reinforced Safety Vest",icon:"🦺",cls:"ops",desc:"At the start of combat, gain 2 Dexterity.",
    onCombatStart:(g)=>applyStatus(g,g.hero,'Dexterity',2)},
  /* --- AWS-themed (scaling / strength) --- */
  autoscaler:{name:"Elastic Load Balancer",icon:"⚖️",cls:"aws",desc:"Every turn after turn 3, gain 1 Strength.",
    onTurnStart:(g)=>{if(g.turn>3)applyStatus(g,g.hero,'Strength',1);}},
  gpu:{name:"GPU Cluster",icon:"🖲️",cls:"aws",desc:"The first attack you play each turn deals 3 extra damage.",
    onTurnStart:(g)=>{g._gpuReady=true;}},
  /* --- PXT-themed (heal / sustain) --- */
  ergochair:{name:"Ergonomic Chair",icon:"🪑",cls:"pxt",desc:"At the start of each turn, heal 2 HP.",
    onTurnStart:(g)=>healHero(g,2)},
  eap:{name:"Employee Assistance",icon:"🧘",cls:"pxt",desc:"Whenever you fall below half HP, heal 8 HP (once per combat).",
    onCombatStart:(g)=>{g._eapUsed=false;}},
  /* --- L&D-themed (draw / cards) --- */
  wiki:{name:"Internal Wiki",icon:"📖",cls:"lnd",desc:"At the start of combat, draw 1 extra card each of the first 2 turns.",
    onTurnStart:(g)=>{if(g.turn<=2)drawCards(g,1);}},
  bootcamp:{name:"Bootcamp Badge",icon:"🎖️",cls:"lnd",desc:"The first card you play each turn costs 1 less energy.",
    onTurnStart:(g)=>{g._discountReady=true;}},
};
const STARTER_RELIC_KEYS = ["coffee_relic","bar_raiser","wb_doc","peccy","monitor","cart","handbook"];
// Class-themed relic pools (weighted higher for the matching class in rollRelic)
const CLASS_RELICS = {
  ops:["forklift","safetyvest"], aws:["autoscaler","gpu"],
  pxt:["ergochair","eap"], lnd:["wiki","bootcamp"]
};

/* ============================================================
   ENEMIES
   ============================================================ */
const ENEMIES = {
  scope:{name:"Scope Creep",img:"enemy_scope_creep.png",hp:[44,50],
    moves:[
      {t:'atk',v:8,name:"Feature Request"},
      {t:'atk',v:5,name:"Requirement Add",buf:{Strength:1}},
      {t:'buf',name:"Sprawl",self:{Strength:2}},
    ]},
  reorg:{name:"The Reorg",img:"enemy_reorg.png",hp:[48,55],
    moves:[
      {t:'atk',v:10,name:"Restructure"},
      {t:'dbf',name:"Confusion",deb:{Weak:2}},
      {t:'def',v:8,name:"Shuffle"},
    ]},
  sev2:{name:"SEV-2 Outage",img:"enemy_sev2.png",hp:[40,46],
    moves:[
      {t:'atk',v:6,mult:2,name:"Cascade Failure"},
      {t:'dbf',name:"Alert Storm",deb:{Vulnerable:2}},
      {t:'atk',v:12,name:"Total Outage"},
    ]},
  peak:{name:"Peak Season",img:"enemy_peak_season.png",hp:[70,80],
    moves:[
      {t:'atk',v:14,name:"Volume Surge"},
      {t:'def',v:10,name:"Fortify Warehouse",self:{Strength:1}},
      {t:'atk',v:8,name:"Double Shift",aoe:true},
    ]},
};
const ELITES = {
  escalation:{name:"Tier-1 Escalation",img:"elite_escalation.png",hp:[95,105],elite:true,
    moves:[
      {t:'atk',v:16,name:"Angry Customer"},
      {t:'buf',name:"Rally Support",self:{Strength:2}},
      {t:'atk',v:8,name:"Ticket Flood",aoe:true},
      {t:'dbf',name:"Frustration",deb:{Weak:2,Vulnerable:2}},
    ]},
};
const BOSS = {
  qbr:{name:"Peccy Prime",img:"boss_level1.png",hp:[150,150],boss:true,
    moves:[
      {t:'atk',v:20,name:"Red Metrics Slam"},
      {t:'buf',name:"Sandbag the Forecast",self:{Strength:2}},
      {t:'atk',v:12,name:"Deep Dive Grilling",aoe:true},
      {t:'dbf',name:"Raise the Bar",deb:{Vulnerable:3,Weak:2}},
      {t:'def',v:18,name:"Spin the Narrative",self:{Strength:1}},
    ]},
  reinvention:{name:"The OP2 Leviathan",img:"boss_level2.png",hp:[220,220],boss:true,
    moves:[
      {t:'atk',v:26,name:"Aggressive Goal-Setting"},
      {t:'buf',name:"Stretch Targets",self:{Strength:3}},
      {t:'atk',v:12,name:"Doc Review Marathon",pierce:true},
      {t:'dbf',name:"Perpetual Reorg",deb:{Vulnerable:3,Weak:3,Frail:2}},
      {t:'def',v:24,name:"Realign Priorities",self:{Strength:2}},
      {t:'atk',v:10,mult:2,name:"Roadmap Barrage",aoe:true},
    ]},
};
/* Act 2 enemies — tougher variants */
const ENEMIES2 = {
  rto:{name:"Return-to-Office Mandate",img:"enemy_rto.png",hp:[62,70],
    moves:[
      {t:'atk',v:12,name:"Badge In"},
      {t:'dbf',name:"Commute Fatigue",deb:{Weak:2,Frail:2}},
      {t:'atk',v:9,name:"Mandatory Sync",aoe:true},
    ]},
  auditor:{name:"The Metrics Auditor",img:"enemy_auditor.png",hp:[58,66],
    moves:[
      {t:'atk',v:10,name:"Red Ink"},
      {t:'dbf',name:"Under Review",deb:{Vulnerable:3}},
      {t:'buf',name:"Escalate Findings",self:{Strength:3}},
    ]},
  legacy:{name:"Legacy System",img:"enemy_legacy.png",hp:[95,105],
    moves:[
      {t:'atk',v:18,name:"Cascading Failure"},
      {t:'def',v:14,name:"Technical Debt",self:{Strength:2}},
      {t:'atk',v:9,mult:2,name:"Deprecated Call"},
    ]},
};
const ELITES2 = {
  acquisition:{name:"The Acquisition",img:"elite_acquisition.png",hp:[130,140],elite:true,
    moves:[
      {t:'atk',v:20,name:"Hostile Takeover"},
      {t:'buf',name:"Synergy Realized",self:{Strength:4}},
      {t:'atk',v:12,name:"Restructure & Cut",aoe:true},
      {t:'dbf',name:"Culture Clash",deb:{Weak:3,Vulnerable:3}},
    ]},
};

/* ============================================================
   GAME STATE
   ============================================================ */
let g = null;

const SAVE_KEY="peakseason_save_v1";
function saveGame(){
  if(!g||!g.map) return;
  try{
    const data={
      cls:g.cls, gold:g.gold, floor:g.floor, baseEnergy:g.baseEnergy,
      party:g.party.map(a=>({clsKey:a.clsKey, maxHp:a.maxHp, hp:a.hp, alive:a.alive})),
      deck:g.deck.map(c=>({k:c.k,upgraded:!!c.upgraded})),
      relics:[...g.relics],
      mapPos:g.mapPos, act:(g.act||1), diff:(g.diff||'normal'), stats:g.stats, potions:(g.potions||[]), revivesUsed:(g.revivesUsed||0),
      // serialize map: type/done/edges only (positions recomputed on render)
      map:g.map.map(row=>row.map(n=>({row:n.row,idx:n.idx,type:n.type,done:!!n.done,edges:[...n.edges],id:n.id}))),
      savedAt:Date.now()
    };
    localStorage.setItem(SAVE_KEY, JSON.stringify(data));
  }catch(e){/* storage may be unavailable on file:// in some browsers */}
}
function hasSave(){
  try{ return !!localStorage.getItem(SAVE_KEY); }catch(e){ return false; }
}
function clearSave(){
  try{ localStorage.removeItem(SAVE_KEY); }catch(e){}
}
function loadGame(){
  let data;
  try{ data=JSON.parse(localStorage.getItem(SAVE_KEY)); }catch(e){ return false; }
  if(!data||!data.map) return false;
  const cls=CLASSES[data.cls]; if(!cls) return false;
  let party;
  if(data.party && data.party.length){
    party=data.party.map(a=>{const al=makeAlly(a.clsKey); al.maxHp=a.maxHp; al.hp=a.hp; al.alive=a.alive!==false; return al;});
  } else if(data.hero){ // back-compat: old single-hero save
    const al=makeAlly(data.cls); al.maxHp=data.hero.maxHp; al.hp=data.hero.hp; party=[al];
  } else { return false; }
  g={
    cls:data.cls, clsDef:CLASSES[party[0].clsKey],
    party, active:0, hero:party[0],
    gold:data.gold, baseEnergy:data.baseEnergy,
    deck:data.deck.map(c=>({id:newId(), k:c.k, upgraded:!!c.upgraded})),
    relics:[...data.relics],
    floor:data.floor, mapPos:data.mapPos, map:null, act:(data.act||1), diff:(data.diff||'normal'), potions:(data.potions||[]), animating:false, revivesUsed:(data.revivesUsed||0),
    stats:(data.stats||{dmgDealt:0,dmgTaken:0,blockGained:0,healed:0,cardsPlayed:0,enemiesSlain:0,combatsWon:0,turns:0,goldEarned:0,bestCombo:0,startedAt:Date.now()})
  };
  // rebuild map with live node objects (positions added at render time)
  g.map=data.map.map(row=>row.map(n=>({row:n.row,idx:n.idx,type:n.type,done:!!n.done,edges:[...n.edges],id:n.id})));
  showMap();
  return true;
}

function makeAlly(clsKey){
  const cls=CLASSES[clsKey];
  return {clsKey, clsDef:cls, name:cls.name, maxHp:cls.maxHp, hp:cls.maxHp, block:0, alive:true,
    status:{Strength:0,Dexterity:0,Vulnerable:0,Weak:0,Frail:0,Regen:0}, powers:{}, taunt:0, id:newId()};
}
function setActive(idx){
  if(!g.party[idx]||!g.party[idx].alive) return;
  g.active=idx; g.hero=g.party[idx];
  if(typeof renderCombat==='function' && $('#combat').classList.contains('active')) renderCombat();
}
function newGame(clsKey, diff){
  clearSave();
  const cls = CLASSES[Array.isArray(clsKey)?clsKey[0]:clsKey];
  const keys = Array.isArray(clsKey)?clsKey:[clsKey];
  const party = keys.map(k=>makeAlly(k));
  const combinedDeck = keys.flatMap(k=>CLASSES[k].starter).map(mk=>makeCard(mk));
  const relics = [...new Set(keys.map(k=>CLASSES[k].startRelic))];
  g = {
    cls:keys[0], clsDef:CLASSES[keys[0]],
    party, active:0, hero:party[0],
    gold:65+rnd(20),
    deck:combinedDeck,
    relics,
    baseEnergy: 4,   // shared pool for a 2-person party
    floor:0, mapPos:null, map:null, act:1, diff:(diff||'normal'),
    potions:[], animating:false, revivesUsed:0,
    stats:{dmgDealt:0,dmgTaken:0,blockGained:0,healed:0,cardsPlayed:0,enemiesSlain:0,combatsWon:0,turns:0,goldEarned:0,bestCombo:0,startedAt:Date.now()},
  };
  buildMap();
  showMap();
}

/* ============================================================
   MAP GENERATION (branching, StS-like)
   ============================================================ */
const ROWS = 10; // 0..9  (row 9 = boss)
function buildMap(){
  const rows=[];
  for(let r=0;r<ROWS;r++){
    let count;
    if(r===ROWS-1) count=1;            // boss
    else if(r===0) count=3;            // start row
    else count=2+rnd(3);               // 2..4
    const nodes=[];
    for(let i=0;i<count;i++){
      nodes.push({row:r,idx:i,edges:[],type:null,done:false,id:`${r}-${i}`});
    }
    rows.push(nodes);
  }
  // assign types
  for(let r=0;r<ROWS;r++){
    for(const n of rows[r]){
      n.type = pickNodeType(r);
    }
  }
  // connect: each node links to 1-2 nodes in next row (nearest by index)
  for(let r=0;r<ROWS-1;r++){
    const cur=rows[r], nxt=rows[r+1];
    for(const n of cur){
      const links = 1 + (Math.random()<0.5?1:0);
      const centerRatio = nxt.length>1 ? (n.idx/(cur.length-1||1)) : 0;
      let target = Math.round(centerRatio*(nxt.length-1));
      const chosen=new Set();
      for(let l=0;l<links;l++){
        let t = clamp(target + (l===0?0:(Math.random()<0.5?-1:1)),0,nxt.length-1);
        chosen.add(t);
      }
      chosen.forEach(t=>{ if(!n.edges.includes(t)) n.edges.push(t); });
    }
    // ensure every next-row node has at least one incoming edge
    nxt.forEach((nn,i)=>{
      const hasIncoming = cur.some(n=>n.edges.includes(i));
      if(!hasIncoming){
        // link nearest current node
        let best=0,bd=99;
        cur.forEach((n,ci)=>{const d=Math.abs((ci/(cur.length-1||1))-(i/(nxt.length-1||1)));if(d<bd){bd=d;best=ci;}});
        cur[best].edges.push(i);
      }
    });
  }
  g.map=rows;
  g.mapPos=null; // not yet entered
}
function pickNodeType(r){
  if(r===ROWS-1) return 'boss';
  if(r===0) return 'combat';
  if(r===ROWS-2) return 'rest';           // rest before boss
  if(r===Math.floor(ROWS/2)) return Math.random()<0.6?'elite':'shop';
  const roll=Math.random();
  if(roll<0.42) return 'combat';
  if(roll<0.58) return 'event';
  if(roll<0.72) return 'elite';
  if(roll<0.84) return 'rest';
  return 'shop';
}
const NODE_ICON={combat:'⚔️',elite:'💀',event:'❓',rest:'🔥',shop:'🛒',boss:'👑'};
const NODE_LABEL={combat:'Combat',elite:'Elite',event:'Event',rest:'Rest Site',shop:'Shop',boss:'BOSS'};
const NODE_DESC={
  combat:'A standard fight. Beat the enemies for gold and a card reward.',
  elite:'A tougher foe — higher risk, but guarantees a relic (mechanism) plus gold.',
  event:'An unpredictable encounter. Make a choice: risk, reward, healing, or a new card.',
  rest:'A quiet moment. Heal ~30% of your max HP, or upgrade a card to make it permanently stronger.',
  shop:'The Company Store. Spend gold on cards, a relic, or pay to remove a card from your deck.',
  boss:'The floor boss — Peccy Prime (Level 1). A brutal fight that ends the level. Come prepared.'
};

function reachableNodes(){
  // if not entered, row 0 all reachable; else children of current node
  if(!g.mapPos) return g.map[0];
  const {row,idx}=g.mapPos;
  const node=g.map[row][idx];
  if(row>=ROWS-1) return [];
  return node.edges.map(i=>g.map[row+1][i]);
}

function renderLegend(){
  const bar=document.getElementById('mapLegend'); if(!bar)return;
  const order=['combat','elite','event','rest','shop','boss'];
  bar.innerHTML='<span class="legendhint">Map key — hover a node for details:</span>'+order.map(t=>
    `<span class="legenditem" title="${NODE_DESC[t]}"><b>${NODE_ICON[t]}</b> ${NODE_LABEL[t]}<span class="legendtip">${NODE_DESC[t]}</span></span>`
  ).join('');
}
function showMap(){
  switchScreen('map');
  startTheme('calm');
  saveGame();
  renderTop();
  renderLegend();
  const canvas=$('#mapCanvas');
  canvas.innerHTML='';
  const W = Math.min(window.innerWidth-40, 900);
  const rowH = 134;
  const H = ROWS*rowH + 40;
  canvas.style.width=W+'px';
  canvas.style.height=H+'px';

  const pos=(r,i,len)=>{
    const x = len===1 ? W/2 : (W*0.12) + (i/(len-1))*(W*0.76);
    const y = H - 40 - r*rowH; // bottom = start
    return {x,y};
  };
  // store positions
  g.map.forEach((row,r)=>row.forEach((n,i)=>{const p=pos(r,i,row.length);n._x=p.x;n._y=p.y;}));

  // edges svg
  const svg=document.createElementNS('http://www.w3.org/2000/svg','svg');
  svg.setAttribute('class','edges');svg.setAttribute('width',W);svg.setAttribute('height',H);
  const reach=reachableNodes().map(n=>n.id);
  g.map.forEach((row,r)=>{
    if(r>=ROWS-1)return;
    row.forEach(n=>{
      n.edges.forEach(ti=>{
        const t=g.map[r+1][ti];
        const line=document.createElementNS('http://www.w3.org/2000/svg','path');
        line.setAttribute('d',`M${n._x},${n._y} C${n._x},${n._y-40} ${t._x},${t._y+40} ${t._x},${t._y}`);
        let cls='edge';
        // open edge = from current node to reachable child
        if(g.mapPos && g.mapPos.row===r && g.mapPos.idx===n.idx) cls+=' open';
        if(!g.mapPos && r===0){} // start row nodes highlighted as nodes
        line.setAttribute('class',cls);
        svg.appendChild(line);
      });
    });
  });
  canvas.appendChild(svg);

  // nodes
  g.map.forEach((row,r)=>row.forEach((n,i)=>{
    const el=document.createElement('div');
    el.className='node node-'+n.type;
    el.style.left=n._x+'px';el.style.top=n._y+'px';
    el.style.backgroundImage=`url('assets/node_${n.type}.png')`;
    el.title=`${NODE_LABEL[n.type]} — ${NODE_DESC[n.type]}`;
    const isReach = reach.includes(n.id);
    const isCurrent = g.mapPos && g.mapPos.row===r && g.mapPos.idx===i;
    if(n.done) el.classList.add('done');
    if(isCurrent) el.classList.add('current');
    if(isReach && !n.done) { el.classList.add('reachable'); el.onclick=()=>enterNode(r,i); }
    canvas.appendChild(el);
    if(n.type==='boss'||n.type==='elite'){
      const lb=document.createElement('div');lb.className='maplabel';
      lb.style.left=n._x+'px';lb.style.top=(n._y)+'px';lb.textContent=NODE_LABEL[n.type];
      canvas.appendChild(lb);
    }
  }));
  // scroll to bottom (start)
  const sc=$('#mapScroll'); sc.scrollTop = sc.scrollHeight;
}

function enterNode(r,i){
  g.mapPos={row:r,idx:i};
  g.floor=r+1;
  const node=g.map[r][i];
  renderTop();
  switch(node.type){
    case 'combat': startCombat(pickCombatEnemies(r),node); break;
    case 'elite': startCombat([spawn((g.act>=2?ELITES2.acquisition:ELITES.escalation))],node); break;
    case 'boss': startCombat([spawn(g.act>=2?BOSS.reinvention:BOSS.qbr)],node); break;
    case 'rest': openRest(node); break;
    case 'shop': openShop(node); break;
    case 'event': openEvent(node); break;
  }
}
function pickCombatEnemies(r){
  if(g.act>=2){
    const light2=[ENEMIES2.rto,ENEMIES2.auditor];
    const heavy2=[ENEMIES2.legacy];
    if(r<=1) return [spawn(pick(light2))];
    if(Math.random()<0.5) return [spawn(pick(light2)),spawn(pick(light2))];
    return [spawn(pick(heavy2))];
  }
  const light=[ENEMIES.scope,ENEMIES.reorg,ENEMIES.sev2]; // <=55 HP
  const heavy=[ENEMIES.peak];                              // 70-80 HP
  if(r<=2){
    // early: single foe, or a pair of LIGHT enemies only (never a heavy)
    if(Math.random()<0.4) return [spawn(ENEMIES.scope),spawn(ENEMIES.reorg)];
    return [spawn(pick([ENEMIES.scope,ENEMIES.reorg]))];
  } else {
    // mid+: a pair is always LIGHT+LIGHT; heavies only appear solo.
    if(Math.random()<0.5) return [spawn(pick(light)),spawn(pick(light))];
    return [spawn(pick([ENEMIES.sev2,ENEMIES.peak]))];
  }
}
const DIFFS = {
  easy:  {name:"Intern",     hpMul:0.8, dmgMul:0.8, gold:1.25, desc:"Gentler foes, more gold. Learn the ropes."},
  normal:{name:"Full-Time",  hpMul:1.0, dmgMul:1.0, gold:1.0,  desc:"The intended challenge."},
  hard:  {name:"Bar Raiser", hpMul:1.25,dmgMul:1.2, gold:0.9,  desc:"Tougher, hungrier enemies. For veterans."},
};
function diffCfg(){ return DIFFS[(g&&g.diff)||'normal']||DIFFS.normal; }
function spawn(def){
  let hp = def.hp[0]===def.hp[1]?def.hp[0]:(def.hp[0]+rnd(def.hp[1]-def.hp[0]+1));
  hp = Math.max(1, Math.round(hp * diffCfg().hpMul));
  return {
    ref:def, name:def.name, img:def.img,
    maxHp:hp, hp, block:0, alive:true,
    status:{Strength:0,Dexterity:0,Vulnerable:0,Weak:0,Frail:0,Exploit:0}, stun:0,
    intent:null, lastMove:-1, id:newId(), elite:!!def.elite, boss:!!def.boss
  };
}
/* enemy helpers (used by AOE / random-target cards & relics) */
function liveEnemies(g){ return g.enemies ? g.enemies.filter(e=>e.alive) : []; }
function randLiveEnemy(g){ const l=liveEnemies(g); return l.length?l[rnd(l.length)]:null; }
/* ---- Last Mile TOKENS (summoned units that act each turn) ---- */
function densityBonus(){ return g.party && g.party.some(a=>a.alive && a.clsKey==='lastmile') ? 1 : 0; }
function summonToken(kind, count){
  count=count||1;
  for(let i=0;i<count;i++){
    if(g.tokens.length>=6){ log(g,'Fleet is full (6 units).'); break; }
    // Driver = attacker, Package = blocker
    g.tokens.push({kind, id:newId(), dmg:kind==='driver'?2:0, block:kind==='package'?3:0});
  }
  log(g,`🚚 Deployed ${count} ${kind==='driver'?'Driver':'Package'} unit(s)!`);
  renderCombat&&renderCombat();
}
async function tokensAct(){
  if(!g.tokens||!g.tokens.length) return;
  for(const tk of g.tokens){
    if(g.combatOver) return;
    if(tk.kind==='driver'){
      const e=randLiveEnemy(g);
      if(e){ dealDamage(g,e,tk.dmg+densityBonus()); if(sfxOn)beep('square',420,0.06,0.05,600); await delay(160); }
    } else if(tk.kind==='package'){
      // Package units shield the active ally
      if(g.hero&&g.hero.alive) gainBlock(g,g.hero,tk.block+densityBonus());
    }
  }
  renderCombat&&renderCombat();
}

/* ============================================================
   COMBAT ENGINE
   ============================================================ */
function startCombat(enemies, node){
  g.enemies=enemies;
  g.node=node;
  // reset every party member for the new combat
  g.party.forEach(a=>{ a.block=0; a.status={Strength:0,Dexterity:0,Vulnerable:0,Weak:0,Frail:0,Regen:0}; a.powers={}; });
  // active = first living ally
  g.active = g.party.findIndex(a=>a.alive); if(g.active<0) g.active=0;
  g.hero = g.party[g.active];
  g.drawPile=shuffle(g.deck.map(c=>({...c})));
  g.hand=[]; g.discard=[]; g.exhaust=[];
  g.turn=0; g.combatOver=false;
  g.tokens=[];   // Last Mile summoned units
  g.powersPlayed=0; g.bonusEnergyNext=0;
  switchScreen('combat');
  renderHero();
  // relic combat start
  g.energy=g.baseEnergy;
  g.relics.forEach(rk=>{const R=RELICS[rk];if(R.onCombatStart)R.onCombatStart(g);});
  log(g,`⚔️ Combat: ${enemies.map(e=>e.name).join(' & ')}`);
  startMusic();
  startTurn(true);
}

function startTurn(first=false){
  g.turn++;
  if(g.stats) g.stats.turns++;
  $('#turnText').textContent=g.turn;
  g.energy = g.baseEnergy + (g.bonusEnergyNext||0);
  g.bonusEnergyNext=0;
  g.cardsPlayedThisTurn=0; g.attacksThisTurn=0; g.powersThisTurn=0;
  g.cardsDrawnThisTurn=0; g._lndBonusUsed=false; g._precedentUsed=false;
  // per-turn effects for EACH living party member (repoint g.hero so helpers target them)
  const _savedActive=g.active;
  g.party.forEach((ally,idx)=>{
    if(!ally.alive) return;
    g.hero=ally;
    ally.block=0;                       // block resets each turn (standard deckbuilder rule)
    ally.thorns=0;                      // Thorns is per-turn (Honeypot re-applies below)
    if(ally.taunt>0){ ally.taunt--; }   // taunt counts down at the start of your turn
    if(ally.status.Regen>0){ healHero(g,ally.status.Regen); ally.status.Regen--; }
    if(ally.powers.reinvent){ applyStatus(g,ally,'Strength',ally.powers.reinvent); }
    if(ally.powers.autoDriver){ summonToken('driver',ally.powers.autoDriver); }
    if(ally.clsKey==='finance'){ g.gold+=3; }               // Compounding innate
    if(ally.powers.buyback){ g.gold+=ally.powers.buyback; }  // Stock Buyback power
    if(ally.powers.honeypot){ ally.thorns=(ally.thorns||0)+ally.powers.honeypot; } // Honeypot Thorns
    if(!first){
      if(ally.clsKey==='ops'){ gainBlock(g,ally,2); }   // Fortress: +2 Block/turn
      if(ally.clsKey==='pxt'){ healHero(g,1); }         // Duty of Care: heal 1/turn
    }
  });
  g.active=_savedActive; g.hero=g.party[g.active] || g.party.find(a=>a.alive);
  if(g.hero && !g.hero.alive){ g.active=g.party.findIndex(a=>a.alive); g.hero=g.party[g.active]; }
  // relic turn start
  g.relics.forEach(rk=>{const R=RELICS[rk];if(R.onTurnStart)R.onTurnStart(g);});
  if(first){
    g.relics.forEach(rk=>{const R=RELICS[rk];if(R.onFirstTurn)R.onFirstTurn(g);});
  }
  // draw — base 5, +1 innate for L&D (Engine), + Certification power
  const draw = 5 + (g.party.some(a=>a.alive&&a.clsKey==='lnd')?1:0) + g.party.reduce((s,a)=>s+(a.alive?(a.powers.extraDraw||0):0),0);
  drawCards(g,draw);
  // set enemy intents
  g.enemies.forEach(e=>{if(e.alive)setIntent(e);});
  renderCombat();
}

function setIntent(e){
  const moves=e.ref.moves;
  let idx;
  do{ idx=rnd(moves.length); }while(moves.length>1 && idx===e.lastMove && Math.random()<0.6);
  e.lastMove=idx;
  const m=moves[idx];
  let dmg=0;
  if(m.t==='atk'){
    dmg = Math.round((m.v + (e.status.Strength||0)) * diffCfg().dmgMul);
    if(e.status.Weak>0) dmg=Math.floor(dmg*0.75);
  }
  // TARGETING: taunt overrides everything; otherwise attack the WEAKER player first.
  const living=g.party.filter(a=>a.alive);
  let tgt=null;
  const taunters=living.filter(a=>(a.taunt||0)>0);
  if(taunters.length){
    // forced onto a taunting ally (lowest HP taunter if several)
    tgt=taunters.sort((x,y)=>x.hp-y.hp)[0];
  } else if(living.length===1){ tgt=living[0]; }
  else if(living.length>1){
    // prefer the weaker ally (by current HP), with a KO bonus and slight stickiness
    const incoming = (m.t==='atk') ? (m.v+(e.status.Strength||0))*(m.mult||1) : 0;
    const scored=living.map(a=>{
      let score = (a.maxHp - a.hp);                  // more missing HP = weaker = juicier
      score += (1-(a.hp/a.maxHp))*60;                // reinforce by HP%
      if(m.t==='atk' && incoming >= a.hp + a.block) score += 150;  // secure a KO
      if(e.lastTargetId===a.id) score += 10;         // focus-fire stickiness
      score += rnd(12);
      return {a, score};
    }).sort((x,y)=>y.score-x.score);
    tgt=scored[0].a;
  }
  e.lastTargetId = tgt?tgt.id:null;
  e.intent={...m, dmg, targetId: tgt?tgt.id:null};
}

function drawCards(g,n){
  const drawn=[];
  for(let i=0;i<n;i++){
    if(g.hand.length>=10){log(g,"✋ Hand is full (10).");break;}
    if(g.drawPile.length===0){
      if(g.discard.length===0)break;
      g.drawPile=shuffle(g.discard);g.discard=[];
    }
    const c=g.drawPile.pop();
    c._dealt=true; drawn.push(c);
    g.hand.push(c);
    g.cardsDrawnThisTurn=(g.cardsDrawnThisTurn||0)+1;
    // L&D Cross-Training: gain Block on each draw
    if(g.hero.powers && g.hero.powers.crossTrain){ g.hero.block+=g.hero.powers.crossTrain; }
  }
  renderPiles();renderHand();
  // staggered draw sound + slide-in
  drawn.forEach((c,idx)=>{
    setTimeout(()=>{
      SFX.draw();
      const el=document.querySelector(`.hand .card[data-cid="${c.id}"]`);
      if(el){el.classList.add('dealt');el.style.animationDelay='0ms';}
    }, idx*70);
    // clear flag after animation so re-renders don't replay it
    setTimeout(()=>{ c._dealt=false; }, idx*70+360);
  });
}

/* ---- damage & block ---- */
function heroAtkBonus(){ return g.hero.status.Strength||0; }
function dealDamage(g,target,amount){
  if(!target)return;
  if(!target.alive && !isAlly(target))return;
  if(isAlly(target) && !target.alive)return;
  let dmg=amount;
  // hero attacking enemy: add strength already added by cards? We add Strength here generically? 
  // We'll add strength at card resolution for hero attacks via a wrapper.
  // Vulnerable on target
  if(target.status.Vulnerable>0) dmg=Math.floor(dmg*1.5);
  dmg=Math.max(0,dmg);
  // block (Pierce ignores block entirely)
  if(target.block>0 && !g._pierce){
    const absorbed=Math.min(target.block,dmg);
    target.block-=absorbed;dmg-=absorbed;
  }
  target.hp-=dmg;
  floatText(target, dmg>0?`-${dmg}`:'0', 'dmg');
  hitFx(target);
  const ally = isAlly(target);
  // Lifesteal: heal active caster for a fraction of damage dealt to enemies
  if(dmg>0 && !ally && g._lifestealPct>0){
    const ls=Math.max(1,Math.floor(dmg*g._lifestealPct));
    g._pendingLifesteal=(g._pendingLifesteal||0)+ls;
  }
  const tfid = target.id;
  if(dmg>0 && g.stats){ if(ally) g.stats.dmgTaken+=dmg; else g.stats.dmgDealt+=dmg; }
  if(dmg>0){
    if(ally){ heroImpact(dmg); }
    else { burst(tfid,'dmg',dmg>=12?16:10); SFX.thud(); }
  } else if(target.block>=0){
    burst(tfid,'block',6);
  }
  // Thorns: retaliate whenever this ally is ATTACKED by an enemy (even if fully blocked)
  if(ally && target.thorns>0 && g._enemyAttacking){
    const re=randLiveEnemy(g);
    if(re){ const th=target.thorns; log(g,`🌵 Thorns hits ${re.name} for ${th}!`); _origDeal(g,re,th); }
  }
  if(!ally && target.hp<=0){ target.hp=0; target.alive=false; SFX.die(); killEnemy(target); }
  if(ally){
    if(!g._eapUsed && g.relics.includes('eap') && target.hp>0 && target.hp<target.maxHp/2){
      g._eapUsed=true; target.hp=clamp(target.hp+8,0,target.maxHp); floatText(target,'+8','heal'); log(g,'🧘 Employee Assistance: heal 8!');
    }
    if(target.hp<=0){ target.hp=0; target.alive=false; onAllyDowned(target); }
  }
  renderCombat();
}
function isAlly(f){ return !!(f && f.clsKey); }
function healParty(pct){ g.party.forEach(a=>{ if(a.alive){ const amt=Math.floor(a.maxHp*pct); a.hp=clamp(a.hp+amt,0,a.maxHp); } }); renderTop(); }
function reviveCostNow(){ return 25 + (g.revivesUsed||0)*20; }   // 25, 45, 65, 85...
function doRevive(ally, hpPct){
  ally.alive=true; ally.hp=Math.max(1,Math.floor(ally.maxHp*(hpPct||0.5)));
  g.revivesUsed=(g.revivesUsed||0)+1;
  if(!g.hero||!g.hero.alive){ g.active=g.party.findIndex(a=>a.alive); g.hero=g.party[g.active]; }
  toast(`${ally.name} revived! ✨`);
  if(sfxOn) beep('sine',660,0.18,0.09,1100);
  renderTop();
}
function revivePartyIfNeeded(){ /* allies stay downed between fights; revive at 30% if all alive rule not needed */ }
function onAllyDowned(ally){
  SFX.die&&SFX.die(); log(g,`💀 ${ally.name} is down!`);
  // if the active ally fell, switch to a surviving one
  if(g.party.every(a=>!a.alive)){ heroDies(); return; }
  if(g.hero===ally || !g.hero.alive){ const i=g.party.findIndex(a=>a.alive); if(i>=0) setActive(i); }
}
// wrapper: hero attacks add Strength
function heroDeal(g,target,base){ dealDamage(g,target,base+heroAtkBonus()); }

function gainBlock(g,who,amount){
  let b=amount;
  if(who===g.hero && g._forkliftReady && g.relics.includes('forklift')){ b+=3; g._forkliftReady=false; }
  if(who.status.Dexterity>0)b+=who.status.Dexterity;
  if(who.status.Frail>0)b=Math.floor(b*0.75);
  b=Math.max(0,b);
  who.block+=b;
  floatText(who,`+${b}`,'blk');
  if(b>0 && g.stats && who===g.hero) g.stats.blockGained+=b;
  if(b>0){ shieldPulse(who); burst(who.id,'block',9); SFX.shield();
    if(who===g.hero && g.hero.powers.culture){ g.hero.hp=clamp(g.hero.hp+g.hero.powers.culture,0,g.hero.maxHp); floatText(g.hero,`+${g.hero.powers.culture}`,'heal'); }
  }
  renderCombat();
}
function healHero(g,n){
  if(g.stats) g.stats.healed+=Math.min(n,g.hero.maxHp-g.hero.hp);
  g.hero.hp=clamp(g.hero.hp+n,0,g.hero.maxHp);
  floatText(g.hero,`+${n}`,'heal');
  if(sfxOn)beep('sine',520,0.14,0.08,880);
  renderTop();renderHero();
}
function applyStatus(g,who,st,n){
  const isDebuff=['Vulnerable','Weak','Frail'].includes(st);
  // Legal innate — Precedent: first debuff applied to an ENEMY each turn is +1 stronger
  if(isDebuff && n>0 && !isAlly(who) && g.party && g.party.some(a=>a.alive&&a.clsKey==='legal') && !g._precedentUsed){
    n+=1; g._precedentUsed=true;
  }
  who.status[st]=(who.status[st]||0)+n;
  if(who.status[st]<0)who.status[st]=0;
  const buff=['Strength','Dexterity','Regen'].includes(st);
  floatText(who,`${st} ${n>0?'+':''}${n}`,buff?'buf':'dmg');
  // Gag Order power — deal damage when a debuff is applied to an enemy
  if(isDebuff && n>0 && !isAlly(who) && g.hero && g.hero.powers && g.hero.powers.gagOrder){
    dealDamage(g, who, g.hero.powers.gagOrder);
  }
  renderCombat();
}

/* ---- playing cards ---- */
let selectedCard=null;
function onCardClick(cardObj, el){
  if(g.animating)return;
  const def=CARDS[cardObj.k];
  const cost=cardCost(cardObj);
  if(cost>g.energy){ toast("Not enough energy ⚡"); return; }
  const needsTarget = def.type==='attack' && !def.aoe;
  if(needsTarget){
    if(selectedCard===cardObj){ selectedCard=null; renderHand(); return; }
    selectedCard=cardObj;
    renderHand();
    toast("Pick a target 🎯");
    // mark enemies targetable
    document.querySelectorAll('.enemy').forEach(en=>en.classList.add('targetable'));
  } else {
    // skills/powers & AOE attacks: fly the card to the hero (or center) then resolve
    const destFid = def.aoe ? null : 'hero';
    animatePlay(cardObj, el, destFid, ()=>resolveCard(cardObj, null));
  }
}
function cardCost(cardObj){
  const def=CARDS[cardObj.k];
  let cost=def.cost;
  if(cardObj.k==='escalate' && g.hand.length===1) return 0;
  // Bootcamp Badge: first card played each turn costs 1 less
  if(g._discountReady && g.relics.includes('bootcamp')) cost=Math.max(0,cost-1);
  return cost;
}
function onEnemyClicked(enemy){
  if(g.animating)return;
  if(!selectedCard)return;
  if(!enemy.alive)return;
  const c=selectedCard; selectedCard=null;
  document.querySelectorAll('.enemy').forEach(en=>en.classList.remove('targetable'));
  const srcEl=document.querySelector(`.hand .card[data-cid="${c.id}"]`);
  animatePlay(c, srcEl, enemy.id, ()=>resolveCard(c, enemy));
}
function resolveCard(cardObj, target){
  const def=CARDS[cardObj.k];
  const cost=cardCost(cardObj);
  if(cost>g.energy){toast("Not enough energy ⚡");return;}
  g.energy-=cost;
  if(g._discountReady && g.relics.includes('bootcamp')) g._discountReady=false; // used this turn
  // remove from hand
  g.hand=g.hand.filter(c=>c.id!==cardObj.id);
  // For hero attacks, we route damage through heroDeal by temporarily wrapping:
  // Simpler: implement each card's play; for attack cards, add Strength inside dealDamage for hero.
  // We'll set a flag so dealDamage adds strength when source is hero attack.
  g._heroAttackContext = (def.type==='attack');
  if(def.type==='attack' && g._gpuReady && g.relics.includes('gpu')){ g._gpuBonus=3; g._gpuReady=false; } else { g._gpuBonus=0; }
  // L&D innate — Applied Learning: first attack each turn gains +1 dmg per 2 cards drawn this turn
  if(def.type==='attack' && g.cls==='lnd' && !g._lndBonusUsed){ g._gpuBonus=(g._gpuBonus||0)+Math.floor((g.cardsDrawnThisTurn||0)/2); g._lndBonusUsed=true; }
  // Lifesteal setup + Overwork HP cost
  g._lifestealPct = def.lifesteal || 0;
  g._pendingLifesteal = 0;
  if(def.pierce) g._pierce=true;   // player Pierce cards ignore enemy Block
  if(def.hpCost){ g.hero.hp=Math.max(1,g.hero.hp-def.hpCost); floatText(g.hero,`-${def.hpCost}`,'dmg'); }
  playEffect(def, cardObj, target);
  g._pierce=false;
  if(g._pendingLifesteal>0){ healHero(g, g._pendingLifesteal); log(g,`🩸 Lifesteal +${g._pendingLifesteal}`); }
  g._lifestealPct=0; g._pendingLifesteal=0;
  g._heroAttackContext=false; g._gpuBonus=0;
  g.cardsPlayedThisTurn++;
  if(g.stats){ g.stats.cardsPlayed++; if(g.cardsPlayedThisTurn>g.stats.bestCombo) g.stats.bestCombo=g.cardsPlayedThisTurn; }
  showCombo(g.cardsPlayedThisTurn);
  // AWS innate — Ramp: every 3rd card played this turn grants +1 Strength
  if(g.cls==='aws' && g.cardsPlayedThisTurn%3===0){ applyStatus(g,g.hero,'Strength',1); log(g,'📈 Ramp: +1 Strength'); }
  if(def.type==='attack'){g.attacksThisTurn++; g.relics.forEach(rk=>{const R=RELICS[rk];if(R.onAttackPlayed)R.onAttackPlayed(g);});}
  if(def.type==='power'){g.powersPlayed++;g.powersThisTurn++;}
  if(def.type==='skill' && g.hero.powers.skillHeal){ healHero(g,g.hero.powers.skillHeal); }
  // discard or exhaust
  if(def.exhaust) g.exhaust.push(cardObj); else g.discard.push(cardObj);
  log(g,`▶️ ${def.name}${cardObj.upgraded?'+':''}`);
  renderCombat();
  checkCombatEnd();
  // Auto end turn once all energy is spent (respects bonus energy from cards/relics)
  if(!g.combatOver && g.energy<=0 && !selectedCard){
    toast("⚡ Out of energy — ending turn");
    setTimeout(()=>{ if(!g.combatOver && g.energy<=0 && $('#combat').classList.contains('active')) endTurn(); }, 700);
  }
}
// central effect runner that injects Strength for hero attacks
function playEffect(def, cardObj, target){
  // Monkey-patch dealDamage for hero attacks to add Strength once per hit
  def.play(g, cardObj, target);
}

// Because card play funcs call dealDamage directly, inject strength there:
const _origDeal = dealDamage;
dealDamage = function(gg,target,amount){
  if(gg._heroAttackContext && target!==gg.hero){
    amount = amount + (gg.hero.status.Strength||0) + (gg._gpuBonus||0);
    gg._gpuBonus=0; // only first hit of the attack gets it
    // Security Threat Model: +25% vs enemies afflicted with Exploit (if a Security ally is in the party)
    if(target && target.status && target.status.Exploit>0 && gg.party && gg.party.some(a=>a.alive&&a.clsKey==='security')){
      amount = Math.round(amount*1.25);
    }
  }
  return _origDeal(gg,target,amount);
};

function endTurn(){
  if(g.combatOver)return;
  selectedCard=null;
  // Second Wind power: heal for each unspent energy at end of turn
  if(g.hero.powers.secondWind && g.energy>0){
    const h=g.hero.powers.secondWind*g.energy;
    healHero(g,h); log(g,`🌬️ Second Wind +${h}`);
  }
  // OSHA Compliance: heal a % of current Block at end of turn
  if(g.hero.powers.blockHealPct && g.hero.block>0){
    const h=Math.floor(g.hero.block*g.hero.powers.blockHealPct/100);
    if(h>0){ healHero(g,h); log(g,`🦺 OSHA +${h}`); }
  }
  // discard hand
  g.discard.push(...g.hand); g.hand=[];
  renderPiles();renderHand();
  // Last Mile tokens act, THEN enemies
  tokensAct().then(()=>{
    if(g.combatOver)return;
    checkCombatEnd();
    if(g.combatOver)return;
    return enemyTurn();
  }).then(()=>{
    if(g.combatOver)return;
    startTurn();
  });
}
function delay(ms){return new Promise(r=>setTimeout(r,ms));}
async function enemyTurn(){
  for(const e of g.enemies){
    if(!e.alive)continue;
    // Security Exploit (poison): tick damage at start of enemy's turn, then decays by 1
    if(e.status.Exploit>0){
      const ex=e.status.Exploit;
      g._pierce=true; dealDamage(g,e,ex); g._pierce=false;   // Exploit ignores block
      if(!e.alive){ continue; }
      e.status.Exploit=Math.max(0,ex-1);
      log(g,`🐛 ${e.name} takes ${ex} Exploit damage.`); renderCombat(); await delay(250);
    }
    if(e.stun>0){
      e.stun--; log(g,`⚖️ ${e.name} is enjoined — turn skipped!`);
      const sw=document.querySelector(`.fighter[data-fid="${e.id}"]`);
      if(sw){ floatText(e,'STUNNED','buf'); }
      await delay(500); continue;
    }
    await delay(650);
    const m=e.intent;
    if(!m)continue;
    if(m.t==='atk'){
      let tgtAlly = g.party.find(a=>a.id===m.targetId && a.alive) || g.party.find(a=>a.alive);
      const taunter=g.party.filter(a=>a.alive&&(a.taunt||0)>0).sort((x,y)=>x.hp-y.hp)[0];
      if(taunter) tgtAlly=taunter;   // taunt redirects the attack, even if played this turn
      if(!tgtAlly) { tickStatuses(e); renderCombat(); continue; }
      // 1) mark the target ally clearly and announce it
      highlightTarget(tgtAlly);
      const cbEl=$('#combat'); if(cbEl) cbEl.classList.add('under-attack-veil');
      log(g,`⚠️ ${e.name} takes aim at ${tgtAlly.name}!`);
      if(sfxOn)beep('sawtooth',150,0.22,0.06,110);
      await delay(1200);           // long pause so you clearly see WHO is targeted
      // 2) enemy wind-up
      const ewrap=document.querySelector(`.fighter[data-fid="${e.id}"]`);
      if(ewrap){ewrap.classList.remove('windup');void ewrap.offsetWidth;ewrap.classList.add('windup');}
      if(sfxOn)beep('sawtooth',200,0.34,0.09,80);
      await delay(850);
      // 3) enemy lunges toward the target as it strikes
      if(ewrap){ewrap.classList.remove('enemylunge');void ewrap.offsetWidth;ewrap.classList.add('enemylunge');}
      // Shred: strip the target's Block before hitting (punishes turtling)
      if(m.shred && tgtAlly && tgtAlly.block>0){
        floatText(tgtAlly, `-${tgtAlly.block} 🛡️`, 'dmg'); tgtAlly.block=0;
        log(g,`✂️ ${e.name} shreds ${tgtAlly.name}'s Block!`); renderCombat(); await delay(300);
      }
      g._enemyAttacking=true;
      const hits=m.mult||1;
      for(let h=0;h<hits;h++){
        let dmg=Math.round((m.v+(e.status.Strength||0))*diffCfg().dmgMul);
        if(e.status.Weak>0)dmg=Math.floor(dmg*0.75);
        if(m.aoe){
          // AOE: hit EVERY living ally (block on one ally won't save the other)
          if(m.pierce) g._pierce=true;
          g.party.forEach(a=>{ if(a.alive){ highlightTarget(a); dealDamage(g,a,dmg); } });
          g._pierce=false;
          if(g.combatOver)return;
        } else {
          if(!tgtAlly||!tgtAlly.alive) tgtAlly=g.party.find(a=>a.alive);
          if(tgtAlly){ highlightTarget(tgtAlly); if(m.pierce)g._pierce=true; dealDamage(g,tgtAlly,dmg); g._pierce=false; }
          if(g.combatOver)return;
        }
        if(hits>1)await delay(420);
      }
      await delay(600);
      clearTargetHighlight();
      g._enemyAttacking=false;
      { const cbEl2=$('#combat'); if(cbEl2) cbEl2.classList.remove('under-attack-veil'); }
      if(m.buf)for(const k in m.buf)applyStatus(g,e,k,m.buf[k]);
    } else if(m.t==='def'){
      gainBlock(g,e,m.v);
      if(m.self)for(const k in m.self)applyStatus(g,e,k,m.self[k]);
    } else if(m.t==='buf'){
      if(m.self)for(const k in m.self)applyStatus(g,e,k,m.self[k]);
    } else if(m.t==='dbf'){
      let dt = g.party.filter(a=>a.alive&&(a.taunt||0)>0).sort((x,y)=>x.hp-y.hp)[0] || g.party.find(a=>a.id===m.targetId && a.alive) || g.party.find(a=>a.alive);
      if(m.deb&&dt)for(const k in m.deb)applyStatus(g,dt,k,m.deb[k]);
    }
    // tick enemy debuffs down
    tickStatuses(e);
    renderCombat();
  }
  await delay(300);
  // tick debuffs on all living allies
  g.party.forEach(a=>{ if(a.alive) tickStatuses(a); });
  renderCombat();
}
function tickStatuses(who){
  ['Vulnerable','Weak','Frail'].forEach(s=>{if(who.status[s]>0)who.status[s]--;});
}

function killEnemy(e){
  if(g.stats) g.stats.enemiesSlain++;
  floatText(e,'✔ done','buf');
  log(g,`💥 ${e.name} defeated!`);
}
function checkCombatEnd(){
  if(g.combatOver)return;
  if(g.enemies.every(e=>!e.alive)){
    g.combatOver=true;
    if(g.stats) g.stats.combatsWon++;
    stopMusic();
    // Downed allies stay down — you can pay gold to revive them in the reward screen.
    g.active=g.party.findIndex(a=>a.alive); if(g.active<0)g.active=0; g.hero=g.party[g.active];
    g.relics.forEach(rk=>{const R=RELICS[rk];if(R.onCombatEnd)R.onCombatEnd(g);});
    setTimeout(()=>winCombat(),500);
  }
}
function heroDies(){
  g.combatOver=true;
  setTimeout(()=>gameOver(false),400);
}

/* ============================================================
   REWARDS
   ============================================================ */
function advanceToAct2(){
  g.act=2; g.floor=0; g.mapPos=null;
  // reward for clearing Level 1: revive downed allies & heal whole party 40%
  g.party.forEach(a=>{ if(!a.alive){ a.alive=true; a.hp=Math.floor(a.maxHp*0.4); } else { a.hp=clamp(a.hp+Math.floor(a.maxHp*0.4),0,a.maxHp); } });
  g.active=g.party.findIndex(a=>a.alive); g.hero=g.party[g.active];
  const overlay=$('#endOverlay');
  overlay.className='overlay-screen end-screen active win';
  $('#endContent').innerHTML=`
    <h1 style="color:var(--gold)">📈 LEVEL 1 CLEARED!</h1>
    <div class="psub" style="font-size:18px;max-width:560px">You survived Peak Season and toppled <b>Peccy Prime</b>. But a bigger disruption looms... <b>The OP2 Leviathan</b> awaits in Level 2. The enemies grow tougher — but so do you.</div>
    <div style="margin:14px 0;color:var(--muted)">Party healed 40% · ${g.deck.length} cards · 🪙 ${g.gold}</div>
    <div class="center-actions"><button class="btn" id="act2Btn">Enter Level 2 →</button></div>`;
  $('#act2Btn').onclick=()=>{ overlay.classList.remove('active'); buildMap(); showMap(); };
  return;
}
function winCombat(){
  const node=g.node;
  node.done=true;
  // gold
  let gold = node.type==='elite'?25+rnd(15): node.type==='boss'?90:12+rnd(12);
  if(g.relics.includes('cart')) gold=Math.floor(gold*1.25);
  gold=Math.round(gold*diffCfg().gold);
  if(g.stats) g.stats.goldEarned+=gold;
  if(node.type==='boss'){
    g.gold+=gold; renderTop();
    if(g.act>=2){ return victory(); }        // Act 2 boss = final victory
    return advanceToAct2();                   // Act 1 boss = continue the run
  }
  g.gold+=gold; renderTop(); SFX.gold();

  const panel=$('#rewardPanel');
  let relicReward = (node.type==='elite') || (Math.random()<0.12);
  let cardOptions = rollCardRewards(3);
  let html=`<h2>Victory! 🎉</h2><div class="psub">Spoils of the sprint</div>`;
  html+=`<div class="rewardgrid">`;
  html+=`<div class="reward-item" id="rw-gold"><span class="ic">🪙</span><div>${gold} Gold<br><small style="color:var(--muted)">Added to your stash</small></div></div>`;
  if(relicReward){
    const rk=rollRelic();
    html+=`<div class="reward-item" id="rw-relic" data-relic="${rk}"><span class="ic">${RELICS[rk].icon}</span><div>${RELICS[rk].name}<br><small style="color:var(--muted)">${RELICS[rk].desc}</small></div></div>`;
  }
  html+=`<div class="reward-item" id="rw-card"><span class="ic">🃏</span><div>Add a card<br><small style="color:var(--muted)">Choose 1 of 3</small></div></div>`;
  // Paid revive — only if a teammate is down. Escalating cost so it's affordable once, costly if leaned on.
  const downed = g.party.filter(a=>!a.alive);
  const reviveCost = reviveCostNow();
  if(downed.length){
    const canAfford = g.gold>=reviveCost;
    downed.forEach(a=>{
      html+=`<div class="reward-item ${canAfford?'':'cantafford'}" id="rw-revive-${a.id}" data-aid="${a.id}"><span class="ic">✨</span><div>Revive ${a.name}<br><small style="color:${canAfford?'var(--gold)':'var(--muted)'}">🪙 ${reviveCost} — restore to 50% HP</small></div></div>`;
    });
  }
  const potionDrop = (node.type==='elite') || (Math.random()<0.4);
  let potionKey=null;
  if(potionDrop){ potionKey=rollPotion();
    html+=`<div class="reward-item" id="rw-potion" data-potion="${potionKey}"><span class="ic">${POTIONS[potionKey].icon}</span><div>${POTIONS[potionKey].name}<br><small style="color:var(--muted)">${POTIONS[potionKey].desc}</small></div></div>`;
  }
  html+=`</div><div class="center-actions"><button class="btn secondary" id="rw-skip">Skip & Continue →</button></div>`;
  panel.innerHTML=html;
  $('#rewardOverlay').classList.add('active');
  // gold auto-claimed visual
  $('#rw-gold').classList.add('taken');
  const relEl=$('#rw-relic');
  if(relEl) relEl.onclick=()=>{ const rk=relEl.dataset.relic; addRelic(rk); relEl.classList.add('taken'); };
  $('#rw-card').onclick=()=>openCardPick(cardOptions,(chosen)=>{
    if(chosen) g.deck.push(chosen);
    $('#rw-card').classList.add('taken');
  });
  const pe=$('#rw-potion');
  if(pe) pe.onclick=()=>{ if(addPotion(pe.dataset.potion)) pe.classList.add('taken'); };
  g.party.filter(a=>!a.alive).forEach(a=>{
    const rv=document.getElementById(`rw-revive-${a.id}`);
    if(rv) rv.onclick=()=>{
      if(rv.classList.contains('taken'))return;
      const cost=reviveCostNow();
      if(g.gold<cost){ toast('Not enough gold 🪙'); return; }
      g.gold-=cost; doRevive(a,0.5);
      rv.classList.add('taken');
      renderTop();
    };
  });
  $('#rw-skip').onclick=()=>{ $('#rewardOverlay').classList.remove('active'); afterNode(); };
}
function rollCardRewards(n){
  const pool=classPool(g.cls);
  const weighted=[];
  pool.forEach(k=>{
    const r=CARDS[k].rarity;
    const w = r==='common'?4 : r==='uncommon'?2 : 1;
    for(let i=0;i<w;i++)weighted.push(k);
  });
  const chosen=new Set();
  while(chosen.size<Math.min(n,pool.length)) chosen.add(pick(weighted));
  return [...chosen].map(k=>makeCard(k, Math.random()<0.15));
}
function rollRelic(){
  const owned=new Set(g.relics);
  // Weighted pool: class-themed relics appear ~3x as often as neutral ones.
  const classPoolKeys=(CLASS_RELICS[g.cls]||[]).filter(k=>!owned.has(k));
  const neutral=STARTER_RELIC_KEYS.filter(k=>!owned.has(k));
  const weighted=[];
  classPoolKeys.forEach(k=>{weighted.push(k,k,k);}); // 3x weight
  neutral.forEach(k=>weighted.push(k));
  if(weighted.length) return pick(weighted);
  // fallback: anything not owned, else any
  const all=[...STARTER_RELIC_KEYS,...Object.values(CLASS_RELICS).flat()].filter(k=>!owned.has(k));
  return all.length?pick(all):pick(STARTER_RELIC_KEYS);
}
function addRelic(rk){
  if(!g.relics.includes(rk)){ g.relics.push(rk); toast(`Gained ${RELICS[rk].name} ${RELICS[rk].icon}`); renderTop(); }
}

/* card picking overlay (rewards, shop preview) */
function openCardPick(cards, cb){
  const panel=$('#cardpickPanel');
  panel.innerHTML=`<h2>Choose a card</h2><div class="psub">Add one to your deck</div>
    <div class="cardrow" id="pickRow"></div>
    <div class="center-actions"><button class="btn ghost" id="pickSkip">Skip</button></div>`;
  const row=$('#pickRow');
  cards.forEach(c=>{
    const wrap=document.createElement('div');wrap.className='pickcard';
    wrap.innerHTML=cardHTML(c);
    wrap.onclick=()=>{ $('#cardpickOverlay').classList.remove('active'); cb(c); };
    row.appendChild(wrap);
  });
  $('#pickSkip').onclick=()=>{ $('#cardpickOverlay').classList.remove('active'); cb(null); };
  $('#cardpickOverlay').classList.add('active');
}

function afterNode(){
  // if boss done -> victory handled elsewhere
  showMap();
}

/* ============================================================
   REST SITE
   ============================================================ */
function openRest(node){
  if(g.relics.includes('handbook')) healHero(g,6);
  const panel=$('#restPanel');
  const healAmt=Math.floor(g.hero.maxHp*0.3);
  const downed = g.party.filter(a=>!a.alive);
  const rCost = reviveCostNow();
  let reviveHtml='';
  if(downed.length){
    reviveHtml = downed.map(a=>`<div class="reward-item" id="rest-rev-${a.id}" data-aid="${a.id}"><span class="ic">✨</span><div>Revive ${a.name}<br><small style="color:var(--gold)">🪙 ${rCost} — back at 60% HP</small></div></div>`).join('');
  }
  panel.innerHTML=`<h2>🔥 Rest Site</h2><div class="psub">A quiet moment before the storm. Choose one.</div>
    <div class="rewardgrid">
      <div class="reward-item" id="restHeal"><span class="ic">❤️</span><div>Rest<br><small style="color:var(--muted)">Heal party ${Math.round(0.30*100)}% HP</small></div></div>
      <div class="reward-item" id="restUpg"><span class="ic">⬆️</span><div>Upgrade a card<br><small style="color:var(--muted)">Make one card permanently stronger</small></div></div>
      ${reviveHtml}
    </div>`;
  $('#restOverlay').classList.add('active');
  $('#restHeal').onclick=()=>{ healParty(0.30); $('#restOverlay').classList.remove('active'); node.done=true; afterNode(); };
  $('#restUpg').onclick=()=>{ openUpgrade(node); };
  downed.forEach(a=>{ const el=document.getElementById(`rest-rev-${a.id}`); if(el) el.onclick=()=>{
    if(el.classList.contains('taken'))return;
    const cost=reviveCostNow();
    if(g.gold<cost){ toast('Not enough gold 🪙'); return; }
    g.gold-=cost; doRevive(a,0.6); el.classList.add('taken');
  };});
}
function openUpgrade(node){
  const upgradable=g.deck.filter(c=>!c.upgraded && hasUpgrade(c.k));
  if(upgradable.length===0){ toast("No cards to upgrade!"); return; }
  const panel=$('#cardpickPanel');
  panel.innerHTML=`<h2>⬆️ Upgrade a card</h2><div class="psub">Choose one to strengthen</div><div class="cardrow" id="pickRow" style="max-height:60vh;overflow:auto"></div><div class="center-actions"><button class="btn ghost" id="pickSkip">Cancel</button></div>`;
  const row=$('#pickRow');
  upgradable.forEach(c=>{
    const wrap=document.createElement('div');wrap.className='pickcard';
    wrap.innerHTML=cardHTML(c,true);
    wrap.onclick=()=>{
      c.upgraded=true;
      $('#cardpickOverlay').classList.remove('active');
      $('#restOverlay').classList.remove('active');
      node.done=true; toast("Card upgraded! ⬆️"); afterNode();
    };
    row.appendChild(wrap);
  });
  $('#pickSkip').onclick=()=>$('#cardpickOverlay').classList.remove('active');
  $('#cardpickOverlay').classList.add('active');
}
function hasUpgrade(k){
  const d=CARDS[k];
  return ['d','b','n','s','v','w','s2'].some(key=>d[key+'u']!==undefined) || d.exhaust;
}

/* ============================================================
   SHOP
   ============================================================ */
function openShop(node){
  const panel=$('#shopPanel');
  const cards=rollCardRewards(5);
  const cardPrices=cards.map(c=>{
    const r=CARDS[c.k].rarity; let base=r==='rare'?110:r==='uncommon'?70:45;
    if(c.upgraded)base+=25; return base+rnd(15);
  });
  const relicKey=rollRelic();
  const relicPrice=140+rnd(40);
  const potionAvail = Math.random()<0.8;
  let html=`<div class="miniheader"><h2>🛒 The Company Store</h2><div class="g">🪙 <span id="shopGold">${g.gold}</span></div></div>
    <div class="psub">Spend your hard-earned gold. Everything must go before Peak.</div>`;
  html+=`<h3 style="margin:8px 0;color:var(--cyan)">Cards</h3><div class="cardrow" id="shopCards"></div>`;
  html+=`<h3 style="margin:18px 0 8px;color:var(--gold)">Mechanism</h3><div class="rewardgrid" id="shopRelics"></div>`;
  html+=`<div class="center-actions" style="margin-top:20px"><button class="btn secondary" id="shopLeave">Leave Shop →</button></div>`;
  panel.innerHTML=html;

  const cardRow=$('#shopCards');
  cards.forEach((c,i)=>{
    const wrap=document.createElement('div');wrap.className='pickcard shopitem';
    wrap.innerHTML=cardHTML(c)+`<div class="price ${g.gold>=cardPrices[i]?'':'cant'}">🪙 ${cardPrices[i]}</div>`;
    wrap.onclick=()=>{
      if(wrap.classList.contains('sold'))return;
      if(g.gold<cardPrices[i]){toast("Not enough gold 🪙");return;}
      g.gold-=cardPrices[i]; g.deck.push(c); wrap.classList.add('sold');
      wrap.style.opacity=.35; wrap.style.pointerEvents='none';
      $('#shopGold').textContent=g.gold; renderTop(); toast(`Bought ${CARDS[c.k].name}!`);
    };
    cardRow.appendChild(wrap);
  });
  const relRow=$('#shopRelics');
  const relEl=document.createElement('div');relEl.className='reward-item shopitem';
  relEl.innerHTML=`<span class="ic">${RELICS[relicKey].icon}</span><div>${RELICS[relicKey].name}<br><small style="color:var(--muted)">${RELICS[relicKey].desc}</small></div><div class="price ${g.gold>=relicPrice?'':'cant'}">🪙 ${relicPrice}</div>`;
  relEl.onclick=()=>{
    if(relEl.classList.contains('sold'))return;
    if(g.gold<relicPrice){toast("Not enough gold 🪙");return;}
    g.gold-=relicPrice; addRelic(relicKey); relEl.classList.add('sold');
    relEl.style.opacity=.35;relEl.style.pointerEvents='none';
    $('#shopGold').textContent=g.gold;
  };
  relRow.appendChild(relEl);
  // card removal service
  const rm=document.createElement('div');rm.className='reward-item shopitem';
  const rmPrice=60;
  rm.innerHTML=`<span class="ic">🗑️</span><div>Card Removal<br><small style="color:var(--muted)">Remove 1 card from your deck</small></div><div class="price ${g.gold>=rmPrice?'':'cant'}">🪙 ${rmPrice}</div>`;
  rm.onclick=()=>{
    if(rm.classList.contains('sold'))return;
    if(g.gold<rmPrice){toast("Not enough gold 🪙");return;}
    openRemove(()=>{ g.gold-=rmPrice; rm.classList.add('sold'); rm.style.opacity=.35;rm.style.pointerEvents='none'; $('#shopGold').textContent=g.gold; renderTop(); });
  };
  relRow.appendChild(rm);
  // revive service (only if a teammate is down)
  g.party.filter(a=>!a.alive).forEach(a=>{
    const rc=reviveCostNow();
    const rvel=document.createElement('div');rvel.className='reward-item shopitem';
    rvel.innerHTML=`<span class="ic">✨</span><div>Revive ${a.name}<br><small style="color:var(--muted)">Back at 50% HP</small></div><div class="price ${g.gold>=rc?'':'cant'}">🪙 ${rc}</div>`;
    rvel.onclick=()=>{
      if(rvel.classList.contains('sold'))return;
      const cost=reviveCostNow();
      if(g.gold<cost){toast("Not enough gold 🪙");return;}
      g.gold-=cost; doRevive(a,0.5); rvel.classList.add('sold'); rvel.style.opacity=.35; rvel.style.pointerEvents='none';
      $('#shopGold').textContent=g.gold; renderTop();
    };
    relRow.appendChild(rvel);
  });
  // potion for sale
  const pk=rollPotion(); const pPrice=45+rnd(20);
  const pel=document.createElement('div');pel.className='reward-item shopitem';
  pel.innerHTML=`<span class="ic">${POTIONS[pk].icon}</span><div>${POTIONS[pk].name}<br><small style="color:var(--muted)">${POTIONS[pk].desc}</small></div><div class="price ${g.gold>=pPrice?'':'cant'}">🪙 ${pPrice}</div>`;
  pel.onclick=()=>{
    if(pel.classList.contains('sold'))return;
    if(g.gold<pPrice){toast("Not enough gold 🪙");return;}
    if(!addPotion(pk))return;
    g.gold-=pPrice; pel.classList.add('sold'); pel.style.opacity=.35; pel.style.pointerEvents='none';
    $('#shopGold').textContent=g.gold; renderTop();
  };
  relRow.appendChild(pel);

  $('#shopLeave').onclick=()=>{ $('#shopOverlay').classList.remove('active'); node.done=true; afterNode(); };
  $('#shopOverlay').classList.add('active');
}
function openRemove(cb){
  const panel=$('#cardpickPanel');
  panel.innerHTML=`<h2>🗑️ Remove a card</h2><div class="psub">Thin your deck — quality over quantity</div><div class="cardrow" id="pickRow" style="max-height:60vh;overflow:auto"></div><div class="center-actions"><button class="btn ghost" id="pickSkip">Cancel</button></div>`;
  const row=$('#pickRow');
  g.deck.forEach(c=>{
    const wrap=document.createElement('div');wrap.className='pickcard';
    wrap.innerHTML=cardHTML(c);
    wrap.onclick=()=>{ g.deck=g.deck.filter(x=>x.id!==c.id); $('#cardpickOverlay').classList.remove('active'); toast("Card removed!"); cb(); };
    row.appendChild(wrap);
  });
  $('#pickSkip').onclick=()=>$('#cardpickOverlay').classList.remove('active');
  $('#cardpickOverlay').classList.add('active');
}

/* ============================================================
   EVENTS
   ============================================================ */
const EVENTS=[
  {title:"The All-Hands Meeting",art:"bg_title.png",
   text:"Leadership announces a bold new vision. You can either take notes diligently (gain a card) or quietly multitask (heal).",
   options:[
     {label:"📝 Take notes (add a random card)",do:()=>{const c=makeCard(pick(classPool(g.cls)));g.deck.push(c);toast(`Added ${CARDS[c.k].name}!`);}},
     {label:"😌 Multitask (heal 12 HP)",do:()=>healHero(g,12)},
   ]},
  {title:"Mysterious Ticket",art:"enemy_sev2.png",
   text:"An unassigned SEV ticket glows ominously in the queue. Investigate for a reward — but it might bite back.",
   options:[
     {label:"🔍 Investigate (50% gold / 50% take 8 dmg)",do:()=>{if(Math.random()<0.5){const gld=40+rnd(30);g.gold+=gld;toast(`Found ${gld} gold!`);}else{dmgHeroOutOfCombat(8);toast("It bit back! -8 HP");}}},
     {label:"🙈 Ignore it (safe)",do:()=>{}},
   ]},
  {title:"Free Swag Booth",art:"enemy_peak_season.png",
   text:"A booth is handing out branded merch. There's a mysterious relic in the pile.",
   options:[
     {label:"🎁 Grab the relic",do:()=>{const rk=rollRelic();addRelic(rk);}},
     {label:"🪙 Take the gift card instead (+35 gold)",do:()=>{g.gold+=35;toast("+35 gold");}},
   ]},
  {title:"The Reorg Rumor",art:"enemy_reorg.png",
   text:"Whispers of a reorg spread. You can lock in your role (upgrade a card) or lay low (remove a card).",
   options:[
     {label:"⬆️ Upgrade a card",do:()=>{queueUpgradeEvent();return true;}},
     {label:"🗑️ Remove a card",do:()=>{queueRemoveEvent();return true;}},
   ]},
  {title:"Working Backwards Session",art:"hero_operations.png",
   text:"You draft a 6-pager. The clarity is invigorating — but it costs you sleep.",
   options:[
     {label:"💪 Push through (+8 max HP)",do:()=>{g.hero.maxHp+=8;g.hero.hp+=8;renderTop();toast("+8 Max HP!");}},
     {label:"😴 Rest instead (heal 20)",do:()=>healHero(g,20)},
   ]},
];
function dmgHeroOutOfCombat(n){ const living=g.party.filter(a=>a.alive); const t=living[rnd(living.length)]||g.hero; t.hp=clamp(t.hp-n,0,t.maxHp); if(t.hp<=0){t.alive=false;} renderTop(); if(g.party.every(a=>!a.alive)){gameOver(false);} else if(!g.hero.alive){const i=g.party.findIndex(a=>a.alive);g.active=i;g.hero=g.party[i];} }
let _eventNode=null;
function openEvent(node){
  _eventNode=node;
  const ev=pick(EVENTS);
  const panel=$('#eventPanel');
  let html=`<img class="evt-art" src="${A}${ev.art}"><h2>${ev.title}</h2><div class="psub" style="max-width:560px;margin:0 auto 18px">${ev.text}</div><div class="center-actions" style="flex-direction:column;align-items:center">`;
  ev.options.forEach((o,i)=>{ html+=`<button class="btn" style="min-width:320px" data-i="${i}">${o.label}</button>`; });
  html+=`</div>`;
  panel.innerHTML=html;
  panel.querySelectorAll('button[data-i]').forEach(b=>{
    b.onclick=()=>{
      const o=ev.options[+b.dataset.i];
      const keepOpen=o.do();
      if(!keepOpen){ $('#eventOverlay').classList.remove('active'); node.done=true; afterNode(); }
      else { $('#eventOverlay').classList.remove('active'); } // sub-overlay will finish node
    };
  });
  $('#eventOverlay').classList.add('active');
}
function queueUpgradeEvent(){
  const upgradable=g.deck.filter(c=>!c.upgraded&&hasUpgrade(c.k));
  if(!upgradable.length){toast("Nothing to upgrade!");_eventNode.done=true;afterNode();return;}
  const panel=$('#cardpickPanel');
  panel.innerHTML=`<h2>⬆️ Upgrade a card</h2><div class="psub">Choose one</div><div class="cardrow" id="pickRow" style="max-height:60vh;overflow:auto"></div>`;
  const row=$('#pickRow');
  upgradable.forEach(c=>{const w=document.createElement('div');w.className='pickcard';w.innerHTML=cardHTML(c,true);
    w.onclick=()=>{c.upgraded=true;$('#cardpickOverlay').classList.remove('active');toast("Upgraded!");_eventNode.done=true;afterNode();};row.appendChild(w);});
  $('#cardpickOverlay').classList.add('active');
}
function queueRemoveEvent(){
  openRemove(()=>{ _eventNode.done=true; afterNode(); });
}

/* ============================================================
   DECK VIEWER
   ============================================================ */
function openDeck(){
  const panel=$('#deckPanel');
  const sorted=[...g.deck].sort((a,b)=>{
    const ta={attack:0,skill:1,power:2}[CARDS[a.k].type]-{attack:0,skill:1,power:2}[CARDS[b.k].type];
    return ta || cardCost(a)-cardCost(b);
  });
  panel.innerHTML=`<div class="miniheader"><h2>🃏 Your Deck (${g.deck.length})</h2><button class="btn ghost" id="deckClose">Close</button></div>
    <div class="cardrow" style="max-height:70vh;overflow:auto" id="deckRow"></div>`;
  const row=$('#deckRow');
  sorted.forEach(c=>{const w=document.createElement('div');w.className='pickcard';w.innerHTML=cardHTML(c);row.appendChild(w);});
  $('#deckClose').onclick=()=>$('#deckOverlay').classList.remove('active');
  $('#deckOverlay').classList.add('active');
}
function openPileViewer(which){
  const panel=$('#deckPanel');
  let list, title, sortIt=true;
  if(which==='draw'){ list=g.drawPile; title='🂠 Draw Pile'; sortIt=true; }   // sorted so it doesn't reveal draw order
  else if(which==='discard'){ list=g.discard; title='🗑️ Discard Pile'; sortIt=false; }
  else { list=g.exhaust; title='🔥 Exhausted'; sortIt=false; }
  let cards=[...list];
  if(sortIt) cards.sort((a,b)=>({attack:0,skill:1,power:2}[CARDS[a.k].type]-{attack:0,skill:1,power:2}[CARDS[b.k].type]) || cardCost(a)-cardCost(b));
  panel.innerHTML=`<div class="miniheader"><h2>${title} (${list.length})</h2><button class="btn ghost" id="deckClose">Close</button></div>`+
    (list.length?`<div class="cardrow" style="max-height:70vh;overflow:auto" id="deckRow"></div>`:`<div class="psub" style="padding:30px">Empty.</div>`);
  if(list.length){ const row=$('#deckRow'); cards.forEach(c=>{const w=document.createElement('div');w.className='pickcard';w.innerHTML=cardHTML(c);row.appendChild(w);}); }
  $('#deckClose').onclick=()=>$('#deckOverlay').classList.remove('active');
  $('#deckOverlay').classList.add('active');
}

/* ============================================================
   RENDERING
   ============================================================ */
function switchScreen(id){
  document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
  $('#'+id).classList.add('active');
  if(id==='combat'){ $('#combat').style.backgroundImage=`url(${A}bg_battle.png)`; }
}
function renderTop(){
  const c=g.clsDef;
  // show party summary on the map (both allies' HP)
  $('#heroFace').src=A+(g.party[0].clsDef?g.party[0].clsDef.img:c.img);
  $('#heroName').textContent=g.party.map(a=>a.name.split(' ')[0]).join(' + ');
  const totHp=g.party.reduce((s,a)=>s+a.hp,0), totMax=g.party.reduce((s,a)=>s+a.maxHp,0);
  $('#hpText').textContent=g.party.map(a=>`${a.hp}/${a.maxHp}`).join('  ');
  $('#hpFill').style.width=(totHp/totMax*100)+'%';
  $('#goldText').textContent=g.gold;
  $('#floorText').textContent=`Level ${g.act||1} · Floor ${g.floor}/${ROWS} · ${diffCfg().name}`;
  renderRelics('#relicTray');
  renderPotions();
}
function renderRelics(sel){
  const tray=$(sel); if(!tray)return; tray.innerHTML='';
  g.relics.forEach(rk=>{
    const R=RELICS[rk];
    const el=document.createElement('div');el.className='relic';el.textContent=R.icon;
    el.innerHTML+=`<div class="tip"><b>${R.name}</b><br>${R.desc}</div>`;
    tray.appendChild(el);
  });
}
function renderHero(){
  const side=$('#heroSide'); side.innerHTML='';
  // figure out how much incoming damage each ally is targeted for this enemy turn
  const incoming={};
  (g.enemies||[]).forEach(e=>{
    if(!e.alive||!e.intent||e.stun>0) return;
    const m=e.intent;
    if(m.t!=='atk') return;
    const dmg=(m.dmg||0)*(m.mult||1);
    if(m.aoe){ g.party.forEach(a=>{ if(a.alive) incoming[a.id]=(incoming[a.id]||0)+dmg; }); }
    else if(m.targetId){ incoming[m.targetId]=(incoming[m.targetId]||0)+dmg; }
  });
  g.party.forEach((ally,idx)=>{
    const el=fighterEl(ally, true, idx);
    if(idx===g.active) el.classList.add('active-ally');
    if(!ally.alive) el.classList.add('downed');
    // persistent TARGETED indicator so you can plan blocks/taunts
    if(ally.alive && incoming[ally.id]>0){
      el.classList.add('targeted-ally');
      const banner=document.createElement('div');
      banner.className='target-banner';
      banner.innerHTML=`🎯 <b>${incoming[ally.id]}</b> incoming`;
      el.appendChild(banner);
    }
    el.addEventListener('click',(e)=>{
      if(ally.alive && !g.animating){ setActive(idx); }
    });
    side.appendChild(el);
  });
  // render summoned Last Mile tokens
  if(g.tokens && g.tokens.length){
    const tray=document.createElement('div'); tray.className='tokentray';
    g.tokens.forEach(tk=>{
      const t=document.createElement('div'); t.className='token '+tk.kind;
      t.innerHTML=(tk.kind==='driver'?'🚘':'📦')+`<span class="tv">${tk.kind==='driver'?('⚔️'+(tk.dmg+densityBonus())):('🛡️'+(tk.block+densityBonus()))}</span>`;
      t.title = tk.kind==='driver'?`Driver — deals ${tk.dmg+densityBonus()} each turn`:`Package — grants ${tk.block+densityBonus()} Block each turn`;
      tray.appendChild(t);
    });
    side.appendChild(tray);
  }
}
function fighterEl(f, isHero, allyIdx){
  const wrap=document.createElement('div');
  wrap.className='fighter '+(isHero?'hero':'enemy');
  wrap.dataset.fid = f.id;   // every fighter (ally or enemy) has a unique id now
  if(!isHero && f.alive) wrap.classList.add('alive');
  const img = isHero?A+(f.clsDef?f.clsDef.img:g.clsDef.img) : A+f.img;
  const hpPct = f.hp/f.maxHp*100;
  let intentHtml='';
  if(!isHero && f.alive && f.intent){
    const m=f.intent;
    let cls='intent', txt='';
    if(m.t==='atk'){ txt=`⚔️ ${m.dmg}${m.mult>1?` ×${m.mult}`:''}${m.aoe?' 💥ALL':''}${m.pierce?' 🗡️PIERCE':''}${m.shred?' ✂️SHRED':''}`; }
    else if(m.t==='def'){cls+=' def';txt=`🛡️ ${m.v}`;}
    else if(m.t==='buf'){cls+=' buf';txt='💪 Buff';}
    else if(m.t==='dbf'){cls+=' dbf';txt='🌀 Debuff';}
    // build a readable description of what this move does
    let desc='';
    if(m.t==='atk'){ desc=`Deals ${m.dmg}${m.mult>1?` ×${m.mult} (${m.dmg*m.mult} total)`:''} damage`; if(m.aoe)desc+=' to ALL allies'; if(m.pierce)desc+=' — ignores Block'; if(m.shred)desc+=' — removes your Block first'; desc+='.'; if(m.buf)desc+=' Then buffs itself.'; }
    else if(m.t==='def'){ desc=`Gains ${m.v} Block${m.self?' and buffs itself':''}.`; }
    else if(m.t==='buf'){ desc='Strengthens itself.'; }
    else if(m.t==='dbf'){ desc='Applies debuffs to you'+(m.deb?': '+Object.keys(m.deb).join(', '):'')+'.'; }
    intentHtml=`<div class="${cls}">${txt}<div class="tip intent-tip"><b>${m.name}</b><br>${desc}</div></div>`;
  }
  const blockBadge = f.block>0?`<div class="block-badge">🛡️${f.block}</div>`:'';
  wrap.innerHTML=`
    <div class="fighter-inner" style="position:relative">
      ${intentHtml}${blockBadge}
      <img src="${img}" alt="${f.name}">
    </div>
    <div class="namep">${f.name}</div>
    <div class="barwrap">
      <div class="ehp"><i style="width:${hpPct}%"></i><span>${f.hp}/${f.maxHp}</span></div>
    </div>
    <div class="statuses">${statusHTML(f)}</div>`;
  if(!isHero && f.alive){
    wrap.querySelector('.fighter-inner').onclick=()=>onEnemyClicked(f);
  }
  return wrap;
}
function statusHTML(f){
  let h='';
  if(f.taunt>0){ h+=`<div class="status buff" style="border-color:var(--orange)">📣 Taunt<div class="tip"><b>Taunt</b><br>${KW.Taunt||''}</div></div>`; }
  const order=['Strength','Dexterity','Regen','Vulnerable','Weak','Frail','Exploit'];
  const icon={Strength:'💪',Dexterity:'🎯',Regen:'💚',Vulnerable:'💥',Weak:'🥀',Frail:'🍂',Exploit:'🐛'};
  order.forEach(s=>{
    if(f.status[s]>0){
      const buff=['Strength','Dexterity','Regen'].includes(s);
      h+=`<div class="status ${buff?'buff':'debuff'}">${icon[s]} ${f.status[s]}<div class="tip"><b>${s}</b><br>${KW[s]||''}</div></div>`;
    }
  });
  if(f.powers&&f.powers.reinvent)h+=`<div class="status buff">🚀 ${f.powers.reinvent}<div class="tip"><b>Re:Invent</b><br>Gain Strength each turn.</div></div>`;
  return h;
}
function renderEnemies(){
  const side=$('#enemySide'); side.innerHTML='';
  g.enemies.forEach(e=>{ if(e.alive) side.appendChild(fighterEl(e,false)); });
}
function renderCombat(){
  renderHero(); renderEnemies();
  $('#cbFace').src=A+(g.hero.clsDef?g.hero.clsDef.img:g.clsDef.img); $('#cbName').textContent='▶ '+g.hero.name;
  $('#cbHpText').textContent=`${g.hero.hp}/${g.hero.maxHp}`;
  $('#cbHpFill').style.width=(g.hero.hp/g.hero.maxHp*100)+'%';
  $('#cbBlock').textContent=g.hero.block;
  $('#cbGold').textContent=g.gold;
  $('#energyText').textContent=g.energy;
  // tint the energy orb to the active ally's class color
  (function(){ const col=g.hero?classColor(g.hero.clsKey):'#ff9900';
    // active-ally ring follows the selected teammate's class color
    const cb=document.getElementById('combat'); if(cb) cb.style.setProperty('--active', col);
    // disable End Turn while an animation/enemy turn is resolving
    const et=document.getElementById('endTurnBtn'); if(et){ et.disabled = !!g.animating || !!g.combatOver; }
  })();
  renderRelics('#cbRelicTray');
  renderPotions();
  renderHand(); renderPiles();
}
function renderHand(){
  const hand=$('#hand'); hand.innerHTML='';
  g.hand.forEach((c,i)=>{
    const el=document.createElement('div');
    el.innerHTML=cardHTML(c);
    const card=el.firstElementChild;
    card.dataset.cid=c.id;
    if(c._dealt) card.classList.add('dealt');
    const cost=cardCost(c);
    if(cost>g.energy) card.classList.add('unplayable');
    if(selectedCard===c) card.classList.add('selected');
    // fan rotation
    const n=g.hand.length;
    const mid=(n-1)/2;
    const rot=(i-mid)*3;
    const lift=Math.abs(i-mid)*4;
    card.style.transform=`rotate(${rot}deg) translateY(${lift}px)`;
    card.onclick=(e)=>{ if(card._dragged){card._dragged=false;return;} onCardClick(c,card); };
    attachDrag(card, c);
    hand.appendChild(card);
  });
}
// Drag-to-play: press and drag a card onto an enemy (or anywhere for non-target cards)
function attachDrag(card, cardObj){
  card.addEventListener('pointerdown',(e)=>{
    if(g.animating||g.combatOver) return;
    if(e.button!==undefined && e.button!==0) return;
    const def=CARDS[cardObj.k];
    const startX=e.clientX, startY=e.clientY;
    let dragging=false, ghost=null;
    const onMove=(ev)=>{
      const dx=ev.clientX-startX, dy=ev.clientY-startY;
      if(!dragging && Math.hypot(dx,dy)>10){
        // begin drag
        if(cardCost(cardObj)>g.energy){ toast('Not enough energy ⚡'); cleanup(); return; }
        dragging=true; card._dragging=true;
        card.style.visibility='hidden';
        ghost=document.createElement('div'); ghost.className='cardfly'; ghost.style.pointerEvents='none';
        const r=card.getBoundingClientRect();
        ghost.style.width=r.width+'px'; ghost.style.height=r.height+'px';
        ghost.innerHTML=cardHTML(cardObj);
        (document.getElementById('fxLayer')||document.body).appendChild(ghost);
        SFX.whoosh&&SFX.whoosh();
        // highlight targets for attacks
        if(def.type==='attack' && !def.aoe) document.querySelectorAll('.enemy').forEach(en=>en.classList.add('targetable'));
      }
      if(dragging && ghost){
        ghost.style.left=(ev.clientX-ghost.offsetWidth/2)+'px';
        ghost.style.top=(ev.clientY-ghost.offsetHeight/2)+'px';
        ghost.style.transform='scale(.62) rotate(-4deg)';
        // hover feedback on enemy under pointer
        document.querySelectorAll('.enemy').forEach(en=>en.classList.remove('drophover'));
        const el=document.elementFromPoint(ev.clientX,ev.clientY);
        const ef=el&&el.closest&&el.closest('.enemy');
        if(ef) ef.classList.add('drophover');
      }
    };
    const onUp=(ev)=>{
      document.removeEventListener('pointermove',onMove);
      document.removeEventListener('pointerup',onUp);
      if(ghost) ghost.remove();
      document.querySelectorAll('.enemy').forEach(en=>en.classList.remove('drophover'));
      if(!dragging){ cleanup(); return; }
      card._dragging=false; card._dragged=true; // suppress the click that follows
      const def=CARDS[cardObj.k];
      const el=document.elementFromPoint(ev.clientX,ev.clientY);
      const ef=el&&el.closest&&el.closest('.enemy');
      if(def.type==='attack' && !def.aoe){
        document.querySelectorAll('.enemy').forEach(en=>en.classList.remove('targetable'));
        // find the enemy object
        let target=null;
        if(ef){ const fid=ef.dataset.fid; target=g.enemies.find(x=>String(x.id)===String(fid)&&x.alive); }
        if(target){ selectedCard=null; const src=document.querySelector(`.hand .card[data-cid="${cardObj.id}"]`); animatePlay(cardObj, src, target.id, ()=>resolveCard(cardObj, target)); }
        else { toast('Drop on an enemy 🎯'); renderHand(); }
      } else {
        // non-target card: dropping anywhere plays it
        const destFid = def.aoe ? null : 'hero';
        const src=document.querySelector(`.hand .card[data-cid="${cardObj.id}"]`);
        animatePlay(cardObj, src, destFid, ()=>resolveCard(cardObj, null));
      }
    };
    const cleanup=()=>{
      document.removeEventListener('pointermove',onMove);
      document.removeEventListener('pointerup',onUp);
      card.style.visibility='';
    };
    document.addEventListener('pointermove',onMove);
    document.addEventListener('pointerup',onUp);
  });
}
const CLASS_LABELS={ops:'🧱 Operations',aws:'📈 AWS',pxt:'💚 PXT',lnd:'📚 L&D',lastmile:'🚚 Last Mile',legal:'⚖️ Legal',finance:'💰 Finance',security:'🛡️ Security'};
const CLASS_COLORS={ops:'#ff9900',aws:'#33ccff',pxt:'#4ade80',lnd:'#a56bff',lastmile:'#2bb7e6',legal:'#ff5c5c',finance:'#ffd24d',security:'#4ade80',generic:'#8fa3b8'};
function classColor(k){return CLASS_COLORS[k]||'#ff9900';}
function cardHTML(c, showUpgrade=false){
  const d=CARDS[c.k];
  const up = showUpgrade || c.upgraded;
  // build text with values
  let txt=d.text;
  txt=txt.replace(/\{(\w+)\}/g,(m,key)=>{
    const base=d[key], upg=d[key+'u'];
    if((c.upgraded||showUpgrade) && upg!==undefined) return `<b>${upg}</b>`;
    return base!==undefined?`${base}`:m;
  });
  txt=kwText(txt);
  const cost = c.k==='escalate' ? d.cost : d.cost;
  const name = d.name + ((c.upgraded)?'+':'');
  return `<div class="card ${d.type} cls-${d.cls||'generic'}">
    <div class="cbadge ${d.cls||'generic'}">${CLASS_LABELS[d.cls]||'⭐ Generic'}</div>
    <div class="cost">${d.cost}</div>
    <div class="art cardart" style="background-image:url('assets/card_${c.k}.png')"></div>
    <div class="cname">${name}</div>
    <div class="ctype">${d.type}</div>
    <div class="ctext"><span>${txt}</span></div>
  </div>`;
}
function renderPiles(){
  $('#drawPile').innerHTML=`🂠 Draw <b>${g.drawPile.length}</b>`;
  $('#discardPile').innerHTML=`🗑️ Discard <b>${g.discard.length}</b>`;
  $('#exhaustPile').innerHTML=`🔥 Exhaust <b>${g.exhaust.length}</b>`;
  $('#drawPile').onclick=()=>openPileViewer('draw');
  $('#discardPile').onclick=()=>openPileViewer('discard');
  $('#exhaustPile').onclick=()=>openPileViewer('exhaust');
}

/* ---- SOUND (Web Audio synth, no files) ---- */
let AC=null, sfxOn=true;
function audio(){ if(!AC){try{AC=new (window.AudioContext||window.webkitAudioContext)();}catch(e){AC=null;}} return AC; }
function beep(type, freq, dur, vol, sweepTo){
  if(!sfxOn)return; const ac=audio(); if(!ac)return;
  if(ac.state==='suspended')ac.resume();
  const t=ac.currentTime;
  const o=ac.createOscillator(), gN=ac.createGain();
  o.type=type; o.frequency.setValueAtTime(freq,t);
  if(sweepTo)o.frequency.exponentialRampToValueAtTime(sweepTo,t+dur);
  gN.gain.setValueAtTime(vol,t);
  gN.gain.exponentialRampToValueAtTime(0.0001,t+dur);
  o.connect(gN).connect(ac.destination); o.start(t); o.stop(t+dur+0.02);
}
function noise(dur,vol){
  if(!sfxOn)return; const ac=audio(); if(!ac)return;
  if(ac.state==='suspended')ac.resume();
  const n=Math.floor(ac.sampleRate*dur), buf=ac.createBuffer(1,n,ac.sampleRate), d=buf.getChannelData(0);
  for(let i=0;i<n;i++)d[i]=(Math.random()*2-1)*(1-i/n);
  const src=ac.createBufferSource(); src.buffer=buf;
  const g2=ac.createGain(); g2.gain.setValueAtTime(vol,ac.currentTime);
  const f=ac.createBiquadFilter(); f.type='highpass'; f.frequency.value=800;
  src.connect(f).connect(g2).connect(ac.destination); src.start();
}
const SFX={
  whoosh:()=>beep('sawtooth',520,0.16,0.10,180),
  thud:()=>{beep('square',150,0.14,0.14,60);noise(0.12,0.12);},
  shield:()=>{beep('triangle',300,0.18,0.12,620);beep('sine',900,0.12,0.06,1200);},
  power:()=>{beep('sine',440,0.28,0.10,880);},
  hurt:()=>{beep('sawtooth',220,0.22,0.16,70);noise(0.16,0.14);},
  draw:()=>beep('triangle',680,0.07,0.05,1000),
  die:()=>beep('square',330,0.4,0.12,80),
  gold:()=>{beep('sine',880,0.09,0.07,1320);beep('sine',1180,0.09,0.05,1600);},
  click:()=>beep('sine',600,0.05,0.05),
};

/* ---- BACKGROUND MUSIC (procedural, Web Audio) ---- */
let musicOn=true, musicTimer=null, musicStep=0, musicGain=null, currentTheme=null;

/* THEME DEFINITIONS */
const THEMES = {
  // Brooding minor-key "corporate dungeon" — combat
  combat:{
    stepDur:0.28, master:0.05,
    bass:[110,110,98,87],           // A2 A2 G2 F2
    arp:[220,262,330,262, 196,247,294,247, 175,220,262,220, 165,220,262,220],
    arpType:'sine', bassType:'triangle', arpVol:0.28, bassVol:0.5, bassEvery:4, arpEvery:1
  },
  // Slow, warm, major-key pads + sparse bell arp — map/shop/rest (calm, hopeful)
  calm:{
    stepDur:0.46, master:0.045,
    // C major feel: C2 G2 A2 F2 (I - V - vi - IV, the "hopeful" progression)
    bass:[65.4, 98.0, 110.0, 87.3],
    // gentle, spacious bell melody (rests represented by 0 = skip)
    arp:[523,0,392,0, 587,0,392,0, 523,0,440,0, 349,0,392,0],
    arpType:'triangle', bassType:'sine', arpVol:0.20, bassVol:0.42, bassEvery:4, arpEvery:1
  }
};

function startTheme(name){
  if(!musicOn)return; const ac=audio(); if(!ac)return;
  if(ac.state==='suspended')ac.resume();
  if(currentTheme===name && musicTimer)return; // already playing this theme
  stopMusic(); // clear any running theme
  currentTheme=name;
  const TH=THEMES[name]; if(!TH)return;
  musicGain=ac.createGain(); musicGain.gain.value=TH.master; musicGain.connect(ac.destination);
  musicStep=0;
  const stepDur=TH.stepDur;
  const tick=()=>{
    if(!musicOn||!AC||currentTheme!==name){return;}
    const t=AC.currentTime;
    const bar=Math.floor(musicStep/4)%4;
    // bass note
    if(musicStep%TH.bassEvery===0){
      const o=AC.createOscillator(),gg=AC.createGain();
      o.type=TH.bassType;o.frequency.value=TH.bass[bar];
      gg.gain.setValueAtTime(0.0001,t);gg.gain.linearRampToValueAtTime(TH.bassVol,t+ (name==='calm'?0.06:0.02));
      gg.gain.exponentialRampToValueAtTime(0.0001,t+stepDur*3.5);
      o.connect(gg).connect(musicGain);o.start(t);o.stop(t+stepDur*3.6);
    }
    // arp / melody note (0 = rest)
    const note=TH.arp[musicStep%TH.arp.length];
    if(note>0 && musicStep%TH.arpEvery===0){
      const o2=AC.createOscillator(),g2=AC.createGain();
      o2.type=TH.arpType;o2.frequency.value=note;
      const atk = name==='calm'?0.04:0.015;
      const rel = name==='calm'?stepDur*1.6:stepDur*0.9;
      g2.gain.setValueAtTime(0.0001,t);g2.gain.linearRampToValueAtTime(TH.arpVol,t+atk);
      g2.gain.exponentialRampToValueAtTime(0.0001,t+rel);
      o2.connect(g2).connect(musicGain);o2.start(t);o2.stop(t+rel+0.05);
      // calm theme: add a soft octave-up shimmer for warmth
      if(name==='calm'){
        const o3=AC.createOscillator(),g3=AC.createGain();
        o3.type='sine';o3.frequency.value=note*2;
        g3.gain.setValueAtTime(0.0001,t);g3.gain.linearRampToValueAtTime(TH.arpVol*0.35,t+atk);
        g3.gain.exponentialRampToValueAtTime(0.0001,t+rel);
        o3.connect(g3).connect(musicGain);o3.start(t);o3.stop(t+rel+0.05);
      }
    }
    musicStep++;
    musicTimer=setTimeout(tick, stepDur*1000);
  };
  tick();
}
// back-compat: startMusic() plays the combat theme
function startMusic(){ startTheme('combat'); }
function stopMusic(){ if(musicTimer){clearTimeout(musicTimer);musicTimer=null;} currentTheme=null; }
function setMusicVolume(v){ if(musicGain)musicGain.gain.value=v; }
function resumeThemeForScreen(){
  if(!musicOn)return;
  if(document.getElementById('combat').classList.contains('active') && g && !g.combatOver){ startTheme('combat'); }
  else if(document.getElementById('map').classList.contains('active')){ startTheme('calm'); }
}

/* ---- COMBO FEEDBACK ---- */
const COMBO_MSGS={3:"Nice combo!",4:"On a roll!",5:"Deep Dive!",6:"Unstoppable!",7:"BAR RAISED!",8:"LEGENDARY!"};
function showCombo(n){
  if(n<3)return;
  const pop=$('#comboPop'); if(!pop)return;
  pop.querySelector('.cnum').textContent=`${n}× COMBO`;
  let msg=COMBO_MSGS[n]||(n>8?"MYTHIC!":"");
  pop.querySelector('.cmsg').textContent=msg;
  pop.className=''; void pop.offsetWidth;
  pop.classList.add('show'); if(n>=7)pop.classList.add('tier3'); else if(n>=5)pop.classList.add('tier2');
  // rising pitch with combo size
  if(sfxOn)beep('triangle',420+n*60,0.12,0.09,760+n*80);
  setTimeout(()=>pop.classList.remove('show'),700);
}

/* ---- PARTICLES ---- */
function burst(fid, kind, count){
  const wrap=document.querySelector(`.fighter[data-fid="${fid}"]`);
  if(!wrap)return;
  const r=wrap.getBoundingClientRect();
  const cx=r.left+r.width/2, cy=r.top+r.height*0.42;
  const cls = kind==='block'?'shard':'spark';
  count=count||12;
  for(let i=0;i<count;i++){
    const p=document.createElement('div');
    p.className='particle '+cls;
    p.style.left=cx+'px'; p.style.top=cy+'px';
    (document.getElementById('fxLayer')||document.body).appendChild(p);
    const ang=Math.random()*Math.PI*2, spd=60+Math.random()*140;
    const dx=Math.cos(ang)*spd, dy=Math.sin(ang)*spd-30;
    const dur=420+Math.random()*260;
    p.animate([
      {transform:'translate(0,0) scale(1)',opacity:1},
      {transform:`translate(${dx}px,${dy+90}px) scale(.3)`,opacity:0}
    ],{duration:dur,easing:'cubic-bezier(.2,.7,.3,1)'}).onfinish=()=>p.remove();
  }
}

/* ---- HERO DAMAGE IMPACT ---- */
function highlightTarget(ally){
  clearTargetHighlight();
  const el=document.querySelector(`.fighter[data-fid="${ally.id}"]`);
  if(el) el.classList.add('under-attack');
}
function clearTargetHighlight(){
  document.querySelectorAll('.fighter.under-attack').forEach(el=>el.classList.remove('under-attack'));
  const cb=document.getElementById('combat'); if(cb) cb.classList.remove('under-attack-veil');
}
function heroImpact(amount){
  const cb=$('#combat');
  if(cb){cb.classList.remove('screenshake');void cb.offsetWidth;cb.classList.add('screenshake');}
  const v=$('#dmgVeil');
  if(v){v.classList.remove('flash');void v.offsetWidth;
    v.style.filter=amount>=15?'brightness(1.4)':'none';v.classList.add('flash');}
  SFX.hurt();
}

/* ---- card-play animation ---- */
function fighterElById(fid){
  return document.querySelector(`.fighter[data-fid="${fid}"]`);
}
function animatePlay(cardObj, srcEl, destFid, done){
  // Fallback: if we can't find source/destination, just resolve immediately.
  const arena = document.querySelector('#combat');
  if(!srcEl || !arena){ done(); return; }
  g.animating=true;
  const def=CARDS[cardObj.k];
  const sRect=srcEl.getBoundingClientRect();
  // destination: a fighter, or screen center for AOE
  let dx, dy;
  const destEl = destFid ? fighterElById(destFid) : null;
  if(destEl){
    const dRect=destEl.getBoundingClientRect();
    dx=dRect.left+dRect.width/2; dy=dRect.top+dRect.height*0.4;
  } else {
    dx=window.innerWidth/2; dy=window.innerHeight*0.42;
  }
  // clone the card into a floating element
  const fly=document.createElement('div');
  fly.className='cardfly';
  fly.style.left=sRect.left+'px';
  fly.style.top=sRect.top+'px';
  fly.style.width=sRect.width+'px';
  fly.style.height=sRect.height+'px';
  fly.innerHTML=cardHTML(cardObj);
  (document.getElementById('fxLayer')||document.body).appendChild(fly);
  SFX.whoosh();
  // hide the original card slot immediately so hand looks like it left
  srcEl.style.visibility='hidden';
  // force reflow then animate toward target
  void fly.offsetWidth;
  const tx=dx-(sRect.left+sRect.width/2);
  const ty=dy-(sRect.top+sRect.height/2);
  const isAttack = def.type==='attack';
  const scale = isAttack?0.55:0.7;
  const rot = isAttack?(Math.random()*30-15):0;
  fly.style.transform=`translate(${tx}px,${ty}px) scale(${scale}) rotate(${rot}deg)`;
  if(isAttack) fly.style.opacity='0.92'; else fly.style.opacity='0.75';
  const cleanup=()=>{
    fly.remove();
    // hero lunge on attack for extra "impact" feel
    if(isAttack && destEl){
      const heroWrap=fighterElById(g.hero.id);
      if(heroWrap){heroWrap.classList.remove('lunge');void heroWrap.offsetWidth;heroWrap.classList.add('lunge');}
    }
    if(def.type==='power'){ castRing(g.hero); SFX.power(); }
    g.animating=false;
    done();
  };
  let called=false;
  const finish=()=>{ if(called)return; called=true; cleanup(); };
  fly.addEventListener('transitionend', finish, {once:true});
  // safety timeout in case transitionend doesn't fire
  setTimeout(finish, 420);
}
function shieldPulse(who){
  const fid = who.id;
  const wrap=fighterElById(fid); if(!wrap)return;
  const inner=wrap.querySelector('.fighter-inner')||wrap;
  const p=document.createElement('div');p.className='shieldpulse';p.textContent='🛡️';
  inner.appendChild(p);
  setTimeout(()=>p.remove(),640);
}
function castRing(who){
  const fid = who.id;
  const wrap=fighterElById(fid); if(!wrap)return;
  const inner=wrap.querySelector('.fighter-inner')||wrap;
  const r=document.createElement('div');r.className='castring';
  inner.appendChild(r);
  setTimeout(()=>r.remove(),520);
}

/* ---- FX ---- */
function floatText(f, text, kind){
  const fid = f.id;
  const el=document.querySelector(`.fighter[data-fid="${fid}"]`);
  if(!el)return;
  const fl=document.createElement('div');fl.className='float '+kind;fl.textContent=text;
  fl.style.left=(40+rnd(60))+'px';fl.style.top='30px';
  el.appendChild(fl);
  setTimeout(()=>fl.remove(),1000);
}
function hitFx(f){
  const fid=f.id;
  const el=document.querySelector(`.fighter[data-fid="${fid}"] img`);
  if(!el)return;
  el.classList.remove('hitflash');void el.offsetWidth;el.classList.add('hitflash');
  const p=el.closest('.fighter');p.classList.remove('shake');void p.offsetWidth;p.classList.add('shake');
}
let logLines=[];
function log(g,msg){ /* combat log removed — no-op */ }
let toastTimer=null;
function toast(msg){
  const t=$('#toast');t.textContent=msg;t.classList.add('show');
  clearTimeout(toastTimer);toastTimer=setTimeout(()=>t.classList.remove('show'),1500);
}

/* ============================================================
   END STATES
   ============================================================ */
function gameOver(win){
  stopMusic();
  clearSave();
  const ov=$('#endOverlay');ov.className='overlay-screen end-screen active '+(win?'win':'lose');
  const st=g.stats||{};
  const mins=Math.max(1,Math.round((Date.now()-(st.startedAt||Date.now()))/60000));
  const statItems=[
    ['🏢','Floor Reached',`${g.floor}/${ROWS}`],
    ['⚔️','Combats Won',st.combatsWon||0],
    ['💀','Enemies Slain',st.enemiesSlain||0],
    ['🃏','Cards Played',st.cardsPlayed||0],
    ['🔥','Best Combo',`${st.bestCombo||0}×`],
    ['💥','Damage Dealt',st.dmgDealt||0],
    ['🩹','Damage Taken',st.dmgTaken||0],
    ['🛡️','Block Gained',st.blockGained||0],
    ['💚','HP Healed',st.healed||0],
    ['🪙','Gold Earned',st.goldEarned||0],
    ['🔄','Turns Taken',st.turns||0],
    ['⏱️','Run Time',`${mins} min`],
  ];
  const grid=statItems.map(([ic,lab,val])=>`<div class="statcell"><div class="si">${ic}</div><div class="sv">${val}</div><div class="sl">${lab}</div></div>`).join('');
  $('#endContent').innerHTML=`
    <h1>${win?'🏆 PROMOTED!':'💀 DEPRIORITIZED'}</h1>
    <div class="psub" style="font-size:17px;max-width:520px">${win?
      'You conquered both levels and defeated The OP2 Leviathan itself. Leadership is in awe. A corner office, a promotion, and an even bigger backlog await. You ARE Peak Season.':
      'The corporate tower claimed another soul. Your deck has been archived in a wiki no one will ever read.'}</div>
    <div style="margin:8px 0 4px;color:var(--muted)">${g.clsDef.name} · ${g.deck.length} cards</div>
    <div class="statgrid">${grid}</div>
    <div class="center-actions"><button class="btn" id="againBtn">Play Again ↻</button></div>`;
  $('#againBtn').onclick=()=>{ ov.classList.remove('active'); buildTitle(); switchScreen('title'); };
}
function victory(){ gameOver(true); }

/* ============================================================
   TITLE / BOOT
   ============================================================ */
function openHelp(){
  const kwIcons={Block:'🛡️',Vulnerable:'💥',Weak:'🥀',Strength:'💪',Dexterity:'🎯',Frail:'🍂',Regen:'💚',Ramp:'📈',Metrics:'📊',Exhaust:'🔥',Lifesteal:'🩸',Overwork:'🩹',Escalate:'📣'};
  const kwOrder=['Block','Strength','Dexterity','Regen','Vulnerable','Weak','Frail','Lifesteal','Overwork','Exhaust','Ramp'];
  const kwHtml=kwOrder.filter(k=>KW[k]).map(k=>`<div class="helprow"><span class="hk">${kwIcons[k]||'•'} ${k}</span><span class="hv">${KW[k]}</span></div>`).join('');
  const panel=document.getElementById('helpPanel');
  panel.innerHTML=`
    <div class="miniheader"><h2>❓ How to Play — Peak Season</h2><button class="btn ghost" id="helpClose">Close</button></div>
    <div class="helpgrid">
      <div class="helpcol">
        <h3>🎯 Goal</h3>
        <p>Climb the corporate tower floor by floor and defeat the boss, <b>The QBR</b>. Each fight, play cards from your hand to survive and win.</p>
        <h3>⚡ Energy</h3>
        <p>You get <b>3 energy</b> each turn. Cards cost energy to play. When your energy hits 0, the turn auto-ends (or hit <b>End Turn</b> early to save energy for effects).</p>
        <h3>🃏 Card Types</h3>
        <div class="helprow"><span class="hk" style="color:#ff8a5c">⚔️ Attack</span><span class="hv">Deals damage. Click a card then click an enemy, or drag it onto the target.</span></div>
        <div class="helprow"><span class="hk" style="color:#4ec3ff">🛡️ Skill</span><span class="hv">Grants Block or utility (heal, draw, buffs). Block absorbs damage but resets each turn.</span></div>
        <div class="helprow"><span class="hk" style="color:#c79bff">⚡ Power</span><span class="hv">A permanent buff for the rest of the fight.</span></div>
      </div>
      <div class="helpcol">
        <h3>📖 Keywords</h3>
        ${kwHtml}
      </div>
      <div class="helpcol">
        <h3>🗺️ The Map</h3>
        <div class="helprow"><span class="hk">⚔️ Combat</span><span class="hv">Standard fight — gold + card reward.</span></div>
        <div class="helprow"><span class="hk">💀 Elite</span><span class="hv">Tougher — guarantees a relic + gold.</span></div>
        <div class="helprow"><span class="hk">❓ Event</span><span class="hv">A choice: risk, reward, healing, or a card.</span></div>
        <div class="helprow"><span class="hk">🔥 Rest</span><span class="hv">Heal ~30% max HP, or upgrade a card.</span></div>
        <div class="helprow"><span class="hk">🛒 Shop</span><span class="hv">Buy cards/relics/potions or remove a card.</span></div>
        <div class="helprow"><span class="hk">👑 Boss</span><span class="hv">The boss — ends the level.</span></div>
        <h3>🧪 Potions & 🎖️ Relics</h3>
        <p><b>Potions</b> (top bar) are one-use — click to use anytime, save them for tough spots. <b>Relics</b> are permanent passive bonuses (hover to read them).</p>
        <h3>💡 Tips</h3>
        <p>Block before big hits (watch enemy <b>intents</b> above their heads). Thin your deck in shops. Heal with intent — sustain has tradeoffs!</p>
      </div>
    </div>`;
  document.getElementById('helpClose').onclick=()=>document.getElementById('helpOverlay').classList.remove('active');
  document.getElementById('helpOverlay').classList.add('active');
}

function buildTitle(){
  $('#title').style.backgroundImage=`url(${A}bg_title.png)`;
  // Continue Run button (only if a save exists)
  const cw=$('#continueWrap'); cw.innerHTML='';
  if(hasSave()){
    let d=null; try{ d=JSON.parse(localStorage.getItem(SAVE_KEY)); }catch(e){}
    if(d){
      const cname=(CLASSES[d.cls]?CLASSES[d.cls].name:d.cls);
      const b=document.createElement('button'); b.className='btn';
      b.style.cssText='font-size:18px;padding:14px 34px';
      b.innerHTML=`▶️ Continue Run <span style="opacity:.8;font-weight:600;font-size:13px">(${cname} · Floor ${d.floor}/${ROWS})</span>`;
      b.onclick=()=>{ if(!loadGame()){ toast('Save was corrupted'); clearSave(); buildTitle(); } };
      cw.appendChild(b);
      const nb=document.createElement('button'); nb.className='btn ghost';
      nb.style.cssText='font-size:12px;padding:6px 14px;margin-left:10px';
      nb.textContent='Abandon & Start New';
      nb.onclick=()=>{ if(confirm('Abandon your current run? This cannot be undone.')){ clearSave(); buildTitle(); } };
      cw.appendChild(nb);
      $('#chooseTag').textContent='— or start a new run —';
    }
  }
  const pick=$('#classpick');pick.innerHTML='';
  g_selected = [];
  Object.entries(CLASSES).forEach(([k,c])=>{
    const el=document.createElement('div');el.className='classcard';el.dataset.k=k;
    el.innerHTML=`<img src="${A}${c.img}">
      <div class="info"><h3>${c.name}</h3>
      <span class="role ${c.roleClass}">${c.role}</span>
      <p>${c.desc}</p>
      <div class="stat">❤️ ${c.maxHp} HP · ⚡ shared</div>
      <div class="stat" style="color:var(--orange2);margin-top:4px">${c.innate||''}</div>
      <div class="pickbadge"></div></div>`;
    el.onclick=()=>togglePick(k, el);
    pick.appendChild(el);
  });
  updatePartyBar();
}
let g_selected = [];
function togglePick(k, el){
  const idx=g_selected.indexOf(k);
  if(idx>=0){ g_selected.splice(idx,1); el.classList.remove('picked'); }
  else {
    if(g_selected.length>=2){ toast('Pick exactly 2 — deselect one first'); return; }
    g_selected.push(k); el.classList.add('picked');
    if(sfxOn && typeof SFX!=='undefined') SFX.click&&SFX.click();
  }
  // renumber badges
  document.querySelectorAll('#classpick .classcard').forEach(card=>{
    const pos=g_selected.indexOf(card.dataset.k);
    const b=card.querySelector('.pickbadge');
    if(b) b.textContent = pos>=0 ? (pos+1) : '';
  });
  updatePartyBar();
}
let g_diff='normal';
function updatePartyBar(){
  const bar=$('#partyStart'); if(!bar)return;
  if(g_selected.length===2){
    const names=g_selected.map(k=>CLASSES[k].name).join(' + ');
    const diffBtns=Object.entries(DIFFS).map(([k,d])=>
      `<button class="diffbtn ${g_diff===k?'sel':''}" data-diff="${k}" title="${d.desc}">${d.name}</button>`).join('');
    bar.innerHTML=`
      <div style="display:flex;flex-direction:column;align-items:center;gap:10px">
        <div style="display:flex;align-items:center;gap:8px">
          <span style="color:var(--muted);font-size:13px">Difficulty:</span>${diffBtns}
        </div>
        <div style="color:var(--muted);font-size:12px" id="diffDesc">${DIFFS[g_diff].desc}</div>
        <button class="btn" id="startRunBtn" style="font-size:18px;padding:14px 30px">⚔️ Start Run — ${names}</button>
      </div>`;
    bar.querySelectorAll('.diffbtn').forEach(b=>b.onclick=()=>{ g_diff=b.dataset.diff; updatePartyBar(); });
    $('#startRunBtn').onclick=()=>newGame([...g_selected], g_diff);
  } else {
    bar.innerHTML=`<span style="color:var(--muted)">Select <b style="color:var(--orange2)">2</b> teammates to form your party (${g_selected.length}/2)</span>`;
  }
}
window.addEventListener('resize',()=>{ if($('#map').classList.contains('active')) showMap(); });
$('#endTurnBtn').onclick=endTurn;
$('#deckBtn').onclick=openDeck;
// Keyboard shortcuts
document.addEventListener('keydown',(e)=>{
  if(e.repeat) return;
  const combatActive = $('#combat').classList.contains('active');
  const anyOverlay = [...document.querySelectorAll('.overlay-screen')].some(o=>o.classList.contains('active'));
  if(e.key==='Escape' && anyOverlay){ document.querySelectorAll('.overlay-screen.active').forEach(o=>o.classList.remove('active')); return; }
  if(anyOverlay) return;
  if(combatActive && !g.combatOver && !g.animating){
    if(e.key==='e' || e.key==='E' || e.key===' '){ e.preventDefault(); endTurn(); }
  }
  if(e.key==='d' || e.key==='D'){ if(g&&g.deck) openDeck(); }
  if(e.key==='?' ){ openHelp(); }
});
(function(){['#helpBtn','#helpBtnCb'].forEach(sel=>{const b=document.querySelector(sel);if(b)b.onclick=openHelp;});})();
// First-time players: auto-open help once
(function(){ try{ if(!localStorage.getItem('peakseason_seen_help')){ localStorage.setItem('peakseason_seen_help','1'); setTimeout(openHelp, 400); } }catch(e){} })();
document.addEventListener('click',()=>{const ac=audio();if(ac&&ac.state==='suspended')ac.resume();},{once:false});
(function(){const b=document.getElementById('sfxToggle');if(b)b.onclick=()=>{sfxOn=!sfxOn;b.textContent=sfxOn?'🔊':'🔇';if(sfxOn)SFX.click();};})();
(function(){const m=document.getElementById('musicToggle');if(m)m.onclick=()=>{musicOn=!musicOn;m.textContent=musicOn?'🎵':'🔕';if(musicOn){resumeThemeForScreen();}else{stopMusic();}};})();
buildTitle();
</script>
</body>
</html>
