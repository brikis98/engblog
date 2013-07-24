require "process"

public_dir = "public"
source_dir = "source"
deploy_dir = "_deploy"
posts_dir = "_posts"
projects_dir = "_projects"
bios_dir = "_bios"
stash_dir = "source/_stash"

git_url = "git@github.com:thinkdeciduous/engblog.git"
git_branch = "gh-pages"
git_master = "master"

desc ""
task :install do
	begin
		require "jekyll"
		require "redcarpet"
		abort("Already installed all blog dependencies! run \"rake deploy\" if you have not already")
	rescue LoadError
		system "gem install jekyll"
		system "gem install redcarpet"
		system "mkdir public"
	end
end

desc ""
task :deploy do
	abort("Blog environment has already been deployed!") if File.directory?(deploy_dir)	
	system "mkdir #{deploy_dir}"
	cd "#{deploy_dir}" do
		# this all needs to be changed once I get the LinkedIn repo setup
		system "git init"
		system "git remote add origin #{git_url}"
		system "git pull origin"
		system "git checkout gh-pages"
	end
end

desc "Move all other posts than the one currently being worked on to a temporary stash location (stash) so regenerating the site happens much more quickly."
task :isolate, :filename do |t, args|
  FileUtils.mkdir("#{source_dir}/#{stash_dir}/") unless File.exist?("#{source_dir}/#{stash_dir}/")
  Dir.glob("#{source_dir}/#{posts_dir}/*.*") do |post|
    FileUtils.mv post, "#{source_dir}/#{stash_dir}/" unless post.include?(args.filename)
  end
end

desc "Move all stashed posts back into the posts directory, ready for site generation."
task :integrate do
  FileUtils.mv Dir.glob("#{source_dir}/#{stash_dir}/*.*"), "#{source_dir}/#{posts_dir}/"
end

desc ""
task :generate do
	system "jekyll build"
	cd "#{deploy_dir}" do
		system "git pull"
	end
	#(Dir["#{deploy_dir}/*"]).each { |f| rm_rf(f) }
	cp_r "#{public_dir}/.", deploy_dir
end

desc ""
task :commit do
	cd "#{deploy_dir}" do
		system "git add -A"
		message = "Site updated at #{Time.now.utc}"
		system "git commit -m \"#{message}\""
		system "git push origin #{git_branch}"
	end
	cd "#{source_dir}/#{posts_dir}" do
		system "git add -A"
		message = "Source upodated at #{Time.now.utc}"
		system "git commit -m \"#{message}\""
		system "git push origin #{git_master}"
	end
end

desc ""
task :preview do 
	cd "#{deploy_dir}" do
		jekyllPid = Process.spawn("jekyll serve")
		trap("INT") {
			Process.kill(9, jekyllPid) rescue Errno::ESRCH
			exit 0
		}
		Process.wait(jekyllPid)
	end
end

