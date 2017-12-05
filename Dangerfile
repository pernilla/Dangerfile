# Sometimes it's a README fix, or something like that - which isn't relevant for
# including in a project's CHANGELOG for example
# declared_trivial = github.pr_title.include? "#trivial"

require 'nokogiri'

# If you wanna try this Dangerscript locally, run: 
#DANGER_GITHUB_API_TOKEN='<your_danger_github_token>' danger pr <path-to-github-pull-request> --verbose

# Just to let people know
warn("PR is classed as Work in Progress") if github.pr_title.include? "WIP"
warn("PR is labeled as DO NOT MERGE") if github.pr_labels.include? "DO NOT MERGE"

# Warn when there is a big PR
warn("PR is BIG!!") if git.lines_of_code > 500

def get_junit_results()

	modules = {}

	# print test results
	Dir.glob("**/TEST-*.xml") do |jfile| 
		module_name = jfile.split("/")[0]

		modules[module_name] ||= {
			name: module_name,
			number_of_tests: 0,
			failures: []
		}

		doc = Nokogiri::XML(open(jfile), nil, Encoding::UTF_8.to_s)

		doc.xpath('//testcase/failure').each do |failure|
			modules[module_name][:failures] << {
				name: failure.parent['name'],
				class_name: failure.parent['classname'],
				time: failure.parent['time'],
				message: failure["message"].gsub("\n", "")
			}
		end

	end

	printable_message = print(modules)
	if(printable_message.empty?) 
		message "Danger did not find any errors"
	else
		fail printable_message
	end

end

def print(modules) 
	output_string = ""

	modules.each do |name, item|
		unless (item[:failures].empty?)

			output_string += "### Found failing tests in Module #{item[:name]}: \n"

			item[:failures].each_with_index do |failure, i|
				output_string += "\n#{i + 1}. __#{failure[:name]} (#{failure[:time]}s)__ \n`#{failure[:message]}`\n"
			end
		end
	end

	return output_string

end

get_junit_results()
