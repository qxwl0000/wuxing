[五行选号器.py .txt](https://github.com/user-attachments/files/30286666/py.txt)
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
import datetime, math, tkinter as tk
from tkinter import messagebox

TIANGAN = "甲乙丙丁戊己庚辛壬癸"
DIZHI = "子丑寅卯辰巳午未申酉戌亥"
WX = {"甲":"木","乙":"木","丙":"火","丁":"火","戊":"土","己":"土","庚":"金","辛":"金","壬":"水","癸":"水"}
ZWX = {"子":"水","丑":"土","寅":"木","卯":"木","辰":"土","巳":"火","午":"火","未":"土","申":"金","酉":"金","戌":"土","亥":"水"}
NW = {"水":[1,6,11,16,21,26,31],"金":[4,9,14,19,24,29,34],"木":[3,8,13,18,23,28,33],"土":[5,10,15,20,25,30,35],"火":[2,7,12,17,22,27,32]}
BW = {"水":[1,6],"金":[4,9],"木":[3,8],"土":[5,10],"火":[2,7]}

def gz_day(dt):
    base = datetime.datetime(1900,1,31)
    d = (dt.date()-base.date()).days
    return TIANGAN[d%10], DIZHI[d%12]

def score(p):
    c = {"火":0,"水":0,"金":0,"木":0,"土":0}
    for g,z in p: c[WX[g]]+=1; c[ZWX[z]]+=0.5
    return c

def pick_f(cnt,dm,lat,n=5):
    cai = {"木":"土","火":"金","土":"水","金":"木","水":"火"}[WX[dm]]
    sc = []
    for wx,nums in NW.items():
        for num in nums:
            s = 0
            if wx==cai: s+=5
            if wx=="水" and cnt["火"]>=3: s+=2
            if wx=="金" and cnt["木"]<1: s+=1
            if wx=="土" and 31<=lat<=32: s+=1
            sc.append((s,num))
    sc.sort(reverse=True)
    return [n for _,n in sc[:n]]

def pick_b(cnt,dm):
    cai = {"木":"土","火":"金","土":"水","金":"木","水":"火"}[WX[dm]]
    return sorted(set(BW[cai]+BW["水"]+BW["金"]))[:2]

def run():
    now = datetime.datetime.now()
    gr,_ = gz_day(now)
    pillars = [(TIANGAN[(now.year-4)%10],DIZHI[(now.year-4)%12]),("X","X"),(gr,"X"),("X","X")]
    cnt = score(pillars)
    f1,pick_f(cnt,gr,31.95); b1 = pick_b(cnt,gr)
    f2 = pick_f(cnt,gr,31.95); b2 = pick_b(cnt,gr)
    if f2==f1: f2=pick_f(cnt,gr,31.95)
    ic = {"火":"火","水":"水","金":"金","木":"木","土":"土"}
    t = ["阴阳五行选号器","="*40,
         f"时间：{now.strftime('%Y-%m-%d %H:%M:%S')}",
         f"地点：南京江宁 (31.95,118.84)",
         f"日主：{gr}({WX[gr]})  财星={ {'木火土金水'[('金木水火土'.index(WX[gr]))] }",
         ""]
    for wx in ["火","水","金","木","土"]:
        v=cnt[wx]; bar="█"*int(v*10)+"░"*(10-int(v*10))
        t.append(f"  {ic[wx]} {wx} │{bar}│ {v:.1f}")
    t += ["","【第一组】财星优先","  前区："+' '.join(f"{n:02d}" for n in f1),"  后区："+' '.join(f"{n:02d}" for n in b1),"",
           "【第二组】五行均衡","  前区："+' '.join(f"{n:02d}" for n in f2),"  后区："+' '.join(f"{n:02d}" for n in b2),"",
           "="*40,"仅供文化娱乐，理性购彩。"]
    root=tk.Tk(); root.withdraw()
    messagebox.showinfo("五行选号结果","\n".join(t))
    root.destroy()

if __name__=="__main__": run()
