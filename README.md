- 👋 Hi, I’m @Fatees911
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
Fatees911/Fatees911 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
#!/bin/bash
ADDR="http://YOUR-NODE-ADDRESS:14265"
TOKEN="YOUR-JWT-TOKEN"

EVENT_ID="f6dbdad416e0470042d3fe429eb0e91683ba171279bce01be6d1d35a9909a981"
RESULT_FILE="shimmer.json"

EVENT_STATUS="$( curl ${ADDR}/api/plugins/participation/events/${EVENT_ID}/status \
  --http1.1 \
  -s \
  -X GET \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer ${TOKEN}" | jq '.data.status' )"

grep -q 'ended' <<< "${EVENT_STATUS}" || { echo "ERROR: event has not ended yet"; exit 1 ; }

curl ${ADDR}/api/plugins/participation/admin/events/${EVENT_ID}/rewards \
  --http1.1 \
  -s \
  -X GET \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer ${TOKEN}" | jq '.data' > ${RESULT_FILE}

printf "Symbol:          %s\n" "$(jq '.symbol' ${RESULT_FILE})"
printf "Milestone index: %s\n" "$(jq '.milestoneIndex' ${RESULT_FILE})"
printf "Total rewards:   %s\n" "$(jq '.totalRewards' ${RESULT_FILE})"
printf "Checksum:        %s\n" "$(jq '.checksum' ${RESULT_FILE})"
