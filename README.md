<h1>Using STM's open data</h1>
<p>First download open data from STM <a href="http://www.stm.info/en/about/developers" target="_blank">web site</a></p>
<h2>To find the closest bus stop</h2>
<code>

$(document).ready(function(){
	var lat = '';
	var lng = '';
	function addZero(i) {
		if (i < 10) {
			i = "0" + i;
		}
		return i;
	}

	function timeNow() {
		var d = new Date();
		var x = document.getElementById('demo');
		var h = addZero(d.getHours());
		var m = addZero(d.getMinutes());
		var s = addZero(d.getSeconds());
		return h + ':' + m + ':' + s;
	}
	function showPosition(pos)
	{
		$('.latitude').text(pos.coords.latitude);
		$('.longitude').text(pos.coords.longitude);
		lat = $('.latitude').text();
		lng = $('.longitude').text();
		var min = 1;
		var _stops = '';
		var time = new Date();
		$.getJSON('data/stops.json',function(stops){
			$.each(stops,function(){
				if(this.stop_lat && this.stop_lon)
				{
					if( min > Math.sqrt( Math.pow((lat - this.stop_lat), 2) + Math.pow((lng - this.stop_lon), 2) ))
					{
						min = Math.sqrt( Math.pow((lat - this.stop_lat), 2) + Math.pow((lng - this.stop_lon), 2) );
						//console.log(this.stop_name+', '+this.stop_id)
						_stops = this;
						//console.log(_stops);
					}
				}
			});
			
			$('.closest_stop').text(_stops.stop_name+ ' ' +_stops.stop_id);
			
			$.getJSON( 'data/stop_times.json')
			.done(function( json ) {
				
				$.each(json.results,function(){
					//console.log(this.stop_id)
					if(_stops.stop_id == this.stop_id)
						console.log(this);
				});
			})
			.fail(function( jqxhr, textStatus, error ) {
				var err = textStatus + ', ' + error;
				console.log( "Request Failed: " + err );
			});
		});
	}
	function initGeoLocation(){
		if (navigator.geolocation) 
		{
			navigator.geolocation.getCurrentPosition(showPosition);
		} 
		else 
		{
			console.log('Geolocation is not supported by this browser.');
		}
	}
	$('.getCurrentLocation').click(function(e){
		e.preventDefault();
		initGeoLocation();
	});
});
</code>
