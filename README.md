This project covers HTTPS SSL configuration and load balancing.
cat > 0-show_the_way.sh << 'EOF'
#!/usr/bin/env bash
# Script that displays information about subdomains of a given domain

display_info() {
    local domain="$1"
    local subdomain="$2"
    local result

    result=$(dig "${subdomain}.${domain}" | grep -A1 'ANSWER SECTION:')
    local record_type
    local destination
    record_type=$(echo "$result" | awk 'NR==2 {print $4}')
    destination=$(echo "$result" | awk 'NR==2 {print $5}')

    echo "The subdomain ${subdomain} is a ${record_type} record and points to ${destination}"
}

domain="$1"
subdomain="$2"

if [ -z "$subdomain" ]; then
    for sub in www lb-01 web-01 web-02; do
        display_info "$domain" "$sub"
    done
else
    display_info "$domain" "$subdomain"
fi
EOF