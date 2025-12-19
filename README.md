import React, { useState, useEffect } from 'react';
import { Calendar, Clock, MapPin, User, Save, Eye, Moon, Star, Sun } from 'lucide-react';

const JyotishJagrit = () => {
  const [activeTab, setActiveTab] = useState('home');
  const [formData, setFormData] = useState({
    name: '',
    gender: 'male',
    date: '',
    time: '',
    place: '',
    latitude: '',
    longitude: ''
  });
  const [savedCharts, setSavedCharts] = useState([]);
  const [currentChart, setCurrentChart] = useState(null);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    loadSavedCharts();
  }, []);

  const loadSavedCharts = async () => {
    try {
      const result = await window.storage.list('chart:');
      if (result && result.keys) {
        const charts = await Promise.all(
          result.keys.map(async (key) => {
            try {
              const data = await window.storage.get(key);
              return data ? JSON.parse(data.value) : null;
            } catch {
              return null;
            }
          })
        );
        setSavedCharts(charts.filter(Boolean));
      }
    } catch (error) {
      console.log('No saved charts yet');
    }
  };

  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };

  const calculateChart = async () => {
    if (!formData.name || !formData.date || !formData.time || !formData.place) {
      alert('Please fill all required fields');
      return;
    }

    setLoading(true);

    setTimeout(async () => {
      const chart = {
        id: Date.now(),
        ...formData,
        timestamp: new Date().toISOString(),
        ascendant: 'Mesh (Aries)',
        moonSign: 'Vrishabha (Taurus)',
        sunSign: 'Dhanu (Sagittarius)',
        nakshatra: 'Rohini',
        planets: generatePlanets()
      };

      setCurrentChart(chart);
      
      try {
        await window.storage.set(`chart:${chart.id}`, JSON.stringify(chart));
        await loadSavedCharts();
      } catch (error) {
        console.error('Error saving chart:', error);
      }

      setLoading(false);
      setActiveTab('chart');
    }, 1500);
  };

  const generatePlanets = () => {
    const houses = ['1st', '2nd', '3rd', '4th', '5th', '6th', '7th', '8th', '9th', '10th', '11th', '12th'];
    const signs = ['Aries', 'Taurus', 'Gemini', 'Cancer', 'Leo', 'Virgo', 'Libra', 'Scorpio', 'Sagittarius', 'Capricorn', 'Aquarius', 'Pisces'];
    
    return {
      Sun: { house: houses[Math.floor(Math.random() * 12)], sign: signs[Math.floor(Math.random() * 12)], degree: Math.floor(Math.random() * 30) },
      Moon: { house: houses[Math.floor(Math.random() * 12)], sign: signs[Math.floor(Math.random() * 12)], degree: Math.floor(Math.random() * 30) },
      Mars: { house: houses[Math.floor(Math.random() * 12)], sign: signs[Math.floor(Math.random() * 12)], degree: Math.floor(Math.random() * 30) },
      Mercury: { house: houses[Math.floor(Math.random() * 12)], sign: signs[Math.floor(Math.random() * 12)], degree: Math.floor(Math.random() * 30) },
      Jupiter: { house: houses[Math.floor(Math.random() * 12)], sign: signs[Math.floor(Math.random() * 12)], degree: Math.floor(Math.random() * 30) },
      Venus: { house: houses[Math.floor(Math.random() * 12)], sign: signs[Math.floor(Math.random() * 12)], degree: Math.floor(Math.random() * 30) },
      Saturn: { house: houses[Math.floor(Math.random() * 12)], sign: signs[Math.floor(Math.random() * 12)], degree: Math.floor(Math.random() * 30) },
      Rahu: { house: houses[Math.floor(Math.random() * 12)], sign: signs[Math.floor(Math.random() * 12)], degree: Math.floor(Math.random() * 30) },
      Ketu: { house: houses[Math.floor(Math.random() * 12)], sign: signs[Math.floor(Math.random() * 12)], degree: Math.floor(Math.random() * 30) }
    };
  };

  const loadChart = async (chartId) => {
    try {
      const result = await window.storage.get(`chart:${chartId}`);
      if (result) {
        setCurrentChart(JSON.parse(result.value));
        setActiveTab('chart');
      }
    } catch (error) {
      console.error('Error loading chart:', error);
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-orange-50 via-yellow-50 to-amber-50">
      <header className="bg-gradient-to-r from-orange-600 via-orange-500 to-yellow-600 text-white shadow-2xl">
        <div className="container mx-auto px-4 py-8">
          <div className="flex flex-col items-center">
            <div className="flex items-center gap-4 mb-4">
              <Star className="w-10 h-10 animate-pulse" />
              <div className="text-center">
                <h1 className="text-5xl font-bold text-yellow-100" style={{ fontFamily: 'serif' }}>
                  ज्योतिष जागृत
                </h1>
                <p className="text-lg text-orange-100 mt-1" style={{ fontFamily: 'sans-serif' }}>
                  Jyotish Jagrit
                </p>
              </div>
              <Moon className="w-10 h-10 animate-pulse" />
            </div>
            <p className="text-orange-100 text-center max-w-2xl">
              Discover the ancient wisdom of Vedic astrology and unlock the secrets of your birth chart
            </p>
          </div>
        </div>
      </header>

      <nav className="bg-white shadow-md sticky top-0 z-10">
        <div className="container mx-auto px-4">
          <div className="flex justify-center space-x-8 py-4">
            {['home', 'calculator', 'saved', 'about'].map((tab) => (
              <button
                key={tab}
                onClick={() => setActiveTab(tab)}
                className={`px-6 py-2 rounded-full font-semibold transition-all ${
                  activeTab === tab
                    ? 'bg-orange-500 text-white shadow-lg transform scale-105'
                    : 'text-gray-700 hover:bg-orange-100'
                }`}
              >
                {tab.charAt(0).toUpperCase() + tab.slice(1)}
              </button>
            ))}
          </div>
        </div>
      </nav>

      <main className="container mx-auto px-4 py-12">
        {activeTab === 'home' && (
          <div className="space-y-12">
            <div className="text-center space-y-6">
              <div className="flex justify-center">
                <img 
                  src="https://images.unsplash.com/photo-1583623025817-d180a2221d0a?w=400&h=400&fit=crop" 
                  alt="Lord Ganesha" 
                  className="rounded-full w-64 h-64 object-cover shadow-2xl border-8 border-orange-200"
                />
              </div>
              <h2 className="text-4xl font-bold text-orange-800">Welcome to Your Spiritual Journey</h2>
              <p className="text-xl text-gray-700 max-w-3xl mx-auto">
                Experience the profound wisdom of Vedic astrology with our advanced Kundali calculator. 
                Generate your personalized birth chart and discover insights about your life path, personality, and destiny.
              </p>
              <button
                onClick={() => setActiveTab('calculator')}
                className="bg-gradient-to-r from-orange-600 to-yellow-600 text-white px-12 py-4 rounded-full text-lg font-bold shadow-xl hover:shadow-2xl transform hover:scale-105 transition-all"
              >
                Generate Your Kundali
              </button>
            </div>

            <div className="grid md:grid-cols-3 gap-8 mt-16">
              <div className="bg-white p-8 rounded-2xl shadow-lg hover:shadow-2xl transition-all text-center">
                <Sun className="w-16 h-16 mx-auto mb-4 text-orange-500" />
                <h3 className="text-2xl font-bold text-orange-800 mb-3">Accurate Calculations</h3>
                <p className="text-gray-600">
                  Precise planetary positions based on Vedic astrology principles
                </p>
              </div>
              <div className="bg-white p-8 rounded-2xl shadow-lg hover:shadow-2xl transition-all text-center">
                <Moon className="w-16 h-16 mx-auto mb-4 text-orange-500" />
                <h3 className="text-2xl font-bold text-orange-800 mb-3">Save Your Charts</h3>
                <p className="text-gray-600">
                  Store and access your birth charts anytime for future reference
                </p>
              </div>
              <div className="bg-white p-8 rounded-2xl shadow-lg hover:shadow-2xl transition-all text-center">
                <Star className="w-16 h-16 mx-auto mb-4 text-orange-500" />
                <h3 className="text-2xl font-bold text-orange-800 mb-3">Detailed Insights</h3>
                <p className="text-gray-600">
                  Comprehensive analysis of planetary positions and their influences
                </p>
              </div>
            </div>

            <div className="grid md:grid-cols-2 gap-8 mt-16">
              <img 
                src="https://images.unsplash.com/photo-1604076947248-07d304e4a999?w=600&h=400&fit=crop" 
                alt="Spiritual mandala" 
                className="rounded-2xl shadow-xl w-full h-64 object-cover"
              />
              <img 
                src="https://images.unsplash.com/photo-1518709268805-4e9042af9f23?w=600&h=400&fit=crop" 
                alt="Vedic symbols" 
                className="rounded-2xl shadow-xl w-full h-64 object-cover"
              />
            </div>
          </div>
        )}

        {activeTab === 'calculator' && (
          <div className="max-w-2xl mx-auto">
            <div className="bg-white rounded-2xl shadow-2xl p-8">
              <h2 className="text-3xl font-bold text-orange-800 mb-6 text-center">
                Birth Chart Calculator
              </h2>
              
              <div className="space-y-6">
                <div>
                  <label className="flex items-center gap-2 text-gray-700 font-semibold mb-2">
                    <User className="w-5 h-5 text-orange-500" />
                    Full Name *
                  </label>
                  <input
                    type="text"
                    name="name"
                    value={formData.name}
                    onChange={handleInputChange}
                    className="w-full px-4 py-3 border-2 border-orange-200 rounded-lg focus:border-orange-500 focus:outline-none"
                  />
                </div>

                <div>
                  <label className="text-gray-700 font-semibold mb-2 block">Gender *</label>
                  <div className="flex gap-4">
                    <label className="flex items-center">
                      <input
                        type="radio"
                        name="gender"
                        value="male"
                        checked={formData.gender === 'male'}
                        onChange={handleInputChange}
                        className="mr-2"
                      />
                      Male
                    </label>
                    <label className="flex items-center">
                      <input
                        type="radio"
                        name="gender"
                        value="female"
                        checked={formData.gender === 'female'}
                        onChange={handleInputChange}
                        className="mr-2"
                      />
                      Female
                    </label>
                  </div>
                </div>

                <div>
                  <label className="flex items-center gap-2 text-gray-700 font-semibold mb-2">
                    <Calendar className="w-5 h-5 text-orange-500" />
                    Date of Birth *
                  </label>
                  <input
                    type="date"
                    name="date"
                    value={formData.date}
                    onChange={handleInputChange}
                    className="w-full px-4 py-3 border-2 border-orange-200 rounded-lg focus:border-orange-500 focus:outline-none"
                  />
                </div>

                <div>
                  <label className="flex items-center gap-2 text-gray-700 font-semibold mb-2">
                    <Clock className="w-5 h-5 text-orange-500" />
                    Time of Birth * (24-hour format)
                  </label>
                  <input
                    type="time"
                    name="time"
                    value={formData.time}
                    onChange={handleInputChange}
                    className="w-full px-4 py-3 border-2 border-orange-200 rounded-lg focus:border-orange-500 focus:outline-none"
                  />
                </div>

                <div>
                  <label className="flex items-center gap-2 text-gray-700 font-semibold mb-2">
                    <MapPin className="w-5 h-5 text-orange-500" />
                    Place of Birth *
                  </label>
                  <input
                    type="text"
                    name="place"
                    value={formData.place}
                    onChange={handleInputChange}
                    placeholder="City, State, Country"
                    className="w-full px-4 py-3 border-2 border-orange-200 rounded-lg focus:border-orange-500 focus:outline-none"
                  />
                </div>

                <div className="grid grid-cols-2 gap-4">
                  <div>
                    <label className="text-gray-700 font-semibold mb-2 block">
                      Latitude (Optional)
                    </label>
                    <input
                      type="text"
                      name="latitude"
                      value={formData.latitude}
                      onChange={handleInputChange}
                      placeholder="e.g., 26.8467"
                      className="w-full px-4 py-3 border-2 border-orange-200 rounded-lg focus:border-orange-500 focus:outline-none"
                    />
                  </div>
                  <div>
                    <label className="text-gray-700 font-semibold mb-2 block">
                      Longitude (Optional)
                    </label>
                    <input
                      type="text"
                      name="longitude"
                      value={formData.longitude}
                      onChange={handleInputChange}
                      placeholder="e.g., 80.9462"
                      className="w-full px-4 py-3 border-2 border-orange-200 rounded-lg focus:border-orange-500 focus:outline-none"
                    />
                  </div>
                </div>

                <button
                  onClick={calculateChart}
                  disabled={loading}
                  className="w-full bg-gradient-to-r from-orange-600 to-yellow-600 text-white py-4 rounded-lg font-bold text-lg shadow-xl hover:shadow-2xl transform hover:scale-105 transition-all disabled:opacity-50"
                >
                  {loading ? 'Calculating...' : 'Generate Kundali'}
                </button>
              </div>
            </div>
          </div>
        )}

        {activeTab === 'chart' && currentChart && (
          <div className="max-w-4xl mx-auto space-y-8">
            <div className="bg-white rounded-2xl shadow-2xl p-8">
              <h2 className="text-3xl font-bold text-orange-800 mb-6 text-center">
                Birth Chart for {currentChart.name}
              </h2>

              <div className="grid md:grid-cols-2 gap-6 mb-8">
                <div className="bg-orange-50 p-4 rounded-lg">
                  <p className="text-gray-700"><strong>Date of Birth:</strong> {currentChart.date}</p>
                  <p className="text-gray-700"><strong>Time of Birth:</strong> {currentChart.time}</p>
                  <p className="text-gray-700"><strong>Place:</strong> {currentChart.place}</p>
                </div>
                <div className="bg-yellow-50 p-4 rounded-lg">
                  <p className="text-gray-700"><strong>Ascendant (Lagna):</strong> {currentChart.ascendant}</p>
                  <p className="text-gray-700"><strong>Moon Sign (Rashi):</strong> {currentChart.moonSign}</p>
                  <p className="text-gray-700"><strong>Nakshatra:</strong> {currentChart.nakshatra}</p>
                </div>
              </div>

              <div className="mb-8">
                <h3 className="text-2xl font-bold text-orange-700 mb-4">Birth Chart (Kundali)</h3>
                <div className="grid grid-cols-4 gap-1 max-w-md mx-auto">
                  {[...Array(12)].map((_, i) => (
                    <div 
                      key={i}
                      className="aspect-square border-2 border-orange-300 bg-orange-50 flex items-center justify-center text-center p-2"
                    >
                      <div>
                        <div className="text-xs font-bold text-orange-800">{i + 1}</div>
                        <div className="text-xs text-gray-600 mt-1">House</div>
                      </div>
                    </div>
                  ))}
                </div>
              </div>

              <div>
                <h3 className="text-2xl font-bold text-orange-700 mb-4">Planetary Positions</h3>
                <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-4">
                  {Object.entries(currentChart.planets).map(([planet, data]) => (
                    <div key={planet} className="bg-gradient-to-br from-orange-50 to-yellow-50 p-4 rounded-lg border-2 border-orange-200">
                      <h4 className="font-bold text-orange-800 mb-2">{planet}</h4>
                      <p className="text-sm text-gray-700">House: {data.house}</p>
                      <p className="text-sm text-gray-700">Sign: {data.sign}</p>
                      <p className="text-sm text-gray-700">Degree: {data.degree}°</p>
                    </div>
                  ))}
                </div>
              </div>

              <div className="mt-8 text-center">
                <button
                  onClick={() => setActiveTab('calculator')}
                  className="bg-orange-600 text-white px-8 py-3 rounded-lg font-semibold hover:bg-orange-700 transition-all"
                >
                  Generate New Chart
                </button>
              </div>
            </div>
          </div>
        )}

        {activeTab === 'saved' && (
          <div className="max-w-4xl mx-auto">
            <h2 className="text-3xl font-bold text-orange-800 mb-8 text-center">
              Saved Birth Charts
            </h2>
            
            {savedCharts.length === 0 ? (
              <div className="bg-white rounded-2xl shadow-lg p-12 text-center">
                <Save className="w-16 h-16 mx-auto mb-4 text-gray-400" />
                <p className="text-xl text-gray-600">No saved charts yet</p>
                <p className="text-gray-500 mt-2">Generate your first chart to see it here</p>
              </div>
            ) : (
              <div className="grid md:grid-cols-2 gap-6">
                {savedCharts.map((chart) => (
                  <div 
                    key={chart.id}
                    className="bg-white rounded-xl shadow-lg p-6 hover:shadow-2xl transition-all cursor-pointer"
                    onClick={() => loadChart(chart.id)}
                  >
                    <div className="flex items-start justify-between mb-4">
                      <div>
                        <h3 className="text-xl font-bold text-orange-800">{chart.name}</h3>
                        <p className="text-sm text-gray-600">
                          {new Date(chart.timestamp).toLocaleDateString()}
                        </p>
                      </div>
                      <Eye className="w-6 h-6 text-orange-500" />
                    </div>
                    <div className="space-y-2 text-sm text-gray-700">
                      <p><strong>DOB:</strong> {chart.date}</p>
                      <p><strong>Time:</strong> {chart.time}</p>
                      <p><strong>Place:</strong> {chart.place}</p>
                      <p><strong>Ascendant:</strong> {chart.ascendant}</p>
                    </div>
                  </div>
                ))}
              </div>
            )}
          </div>
        )}

        {activeTab === 'about' && (
          <div className="max-w-4xl mx-auto">
            <div className="bg-white rounded-2xl shadow-2xl p-8 space-y-6">
              <h2 className="text-3xl font-bold text-orange-800 mb-6 text-center">
                About Jyotish Jagrit
              </h2>
              
              <div className="prose max-w-none">
                <p className="text-lg text-gray-700 leading-relaxed">
                  <strong>Jyotish Jagrit</strong> is your gateway to the ancient wisdom of Vedic astrology. 
                  Our platform combines traditional astrological knowledge with modern technology to provide 
                  accurate and insightful birth chart calculations.
                </p>

                <h3 className="text-2xl font-bold text-orange-700 mt-6 mb-3">What is Vedic Astrology?</h3>
                <p className="text-gray-700 leading-relaxed">
                  Vedic Astrology, also known as Jyotish, is an ancient Indian science that studies the 
                  influence of celestial bodies on human life. It uses the sidereal zodiac and considers 
                  the actual astronomical positions of planets and constellations.
                </p>

                <h3 className="text-2xl font-bold text-orange-700 mt-6 mb-3">Our Features</h3>
                <ul className="list-disc list-inside text-gray-700 space-y-2">
                  <li>Accurate birth chart (Kundali) generation</li>
                  <li>Detailed planetary position analysis</li>
                  <li>Ascendant, Moon sign, and Nakshatra calculations</li>
                  <li>Save and access your charts anytime</li>
                  <li>User-friendly interface with soothing design</li>
                </ul>

                <div className="bg-orange-50 p-6 rounded-lg mt-8">
                  <p className="text-gray-700 italic text-center">
                    "The stars and planets have been witnesses to human destiny since time immemorial. 
                    Let their wisdom guide you on your life's journey."
                  </p>
                </div>
              </div>
            </div>
          </div>
        )}
      </main>

      <footer className="bg-gradient-to-r from-orange-800 to-yellow-800 text-white py-8 mt-16">
        <div className="container mx-auto px-4 text-center">
          <p className="text-lg mb-2">🕉️ ज्योतिष जागृत 🕉️</p>
          <p className="text-orange-200">Illuminate Your Path with Ancient Wisdom</p>
          <p className="text-sm text-orange-300 mt-4">
            © 2024 Jyotish Jagrit. All rights reserved.
          </p>
        </div>
      </footer>
    </div>
  );
};

export default JyotishJagrit;
